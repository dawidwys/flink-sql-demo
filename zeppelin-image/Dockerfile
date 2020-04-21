# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM ubuntu:16.04
MAINTAINER Apache Software Foundation <dev@zeppelin.apache.org>

# `Z_VERSION` will be updated by `dev/change_zeppelin_version.sh`
ENV Z_VERSION="0.9.0"
ENV LOG_TAG="[ZEPPELIN_${Z_VERSION}]:" \
    Z_HOME="/zeppelin" \
    LANG=en_US.UTF-8 \
    LC_ALL=en_US.UTF-8

RUN echo "$LOG_TAG update and install basic packages" && \
    apt-get -y update && \
    apt-get install -y locales && \
    locale-gen $LANG && \
    apt-get install -y software-properties-common && \
    apt -y autoclean && \
    apt -y dist-upgrade && \
    apt-get install -y build-essential

RUN echo "$LOG_TAG install tini related packages" && \
    apt-get install -y wget curl grep sed dpkg && \
    TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
RUN echo "$LOG_TAG Install java8" && \
    apt-get -y update && \
    apt-get install -y openjdk-8-jdk && \
    rm -rf /var/lib/apt/lists/*

# should install conda first before numpy, matploylib since pip and python will be installed by conda
RUN echo "$LOG_TAG Install miniconda2 related packages" && \
    apt-get -y update && \
    apt-get install -y bzip2 ca-certificates \
    libglib2.0-0 libxext6 libsm6 libxrender1 \
    git mercurial subversion && \
    echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh && \
    wget --quiet https://repo.continuum.io/miniconda/Miniconda2-4.2.12-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh
ENV PATH /opt/conda/bin:$PATH

RUN echo "$LOG_TAG Install python related packages" && \
    apt-get -y update && \
    apt-get install -y python-dev python-pip && \
    apt-get install -y gfortran && \
    # numerical/algebra packages
    apt-get install -y libblas-dev libatlas-dev liblapack-dev && \
    # font, image for matplotlib
    apt-get install -y libpng-dev libfreetype6-dev libxft-dev && \
    # for tkinter
    apt-get install -y python-tk libxml2-dev libxslt-dev zlib1g-dev && \
    conda config --set always_yes yes --set changeps1 no && \
    conda update -q conda && \
    conda info -a && \
    conda config --add channels conda-forge && \
    conda install -q numpy=1.13.3 pandas=0.21.1 matplotlib=2.1.1 pandasql=0.7.3 ipython=5.4.1 jupyter_client=5.1.0 ipykernel=4.7.0 bokeh=0.12.10 && \
    pip install -q scipy==0.18.0 ggplot==0.11.5 grpcio==1.8.2 bkzep==0.4.0

COPY zeppelin-master.tgz /tmp/
RUN echo "$LOG_TAG Download Zeppelin binary" && \
    tar -zxf /tmp/zeppelin-master.tgz && \
    rm -rf /tmp/zeppelin-master.tgz

# Configure Flink version
ENV FLINK_VERSION=1.10.0 \
    SCALA_VERSION=2.11

# Prepare environment
ENV FLINK_HOME=$Z_HOME/flink-dist

ENV FLINK_URL_FILE_PATH=flink/flink-${FLINK_VERSION}/flink-${FLINK_VERSION}-bin-scala_${SCALA_VERSION}.tgz
ENV FLINK_TGZ_URL=https://www.apache.org/dyn/closer.cgi?action=download&filename=${FLINK_URL_FILE_PATH}

# Install Flink
RUN set -ex; \
  wget -nv -O flink.tgz "$FLINK_TGZ_URL"; \
  \
  mkdir $FLINK_HOME; \
  tar -xf flink.tgz --strip-components=1 -C $FLINK_HOME; \
  rm flink.tgz;

RUN wget https://repo1.maven.org/maven2/org/apache/flink/flink-connector-hive_${SCALA_VERSION}/${FLINK_VERSION}/flink-connector-hive_${SCALA_VERSION}-${FLINK_VERSION}.jar -O /zeppelin/flink-dist/lib/flink-connector-hive_${SCALA_VERSION}-${FLINK_VERSION}.jar; \
    wget https://repo1.maven.org/maven2/org/apache/flink/flink-csv/${FLINK_VERSION}/flink-csv-${FLINK_VERSION}.jar -O /zeppelin/flink-dist/lib/flink-csv-${FLINK_VERSION}.jar; \
    wget https://repo1.maven.org/maven2/org/apache/hive/hive-exec/2.3.6/hive-exec-2.3.6.jar -O /zeppelin/flink-dist/lib/hive-exec-2.3.6.jar; \
wget -P /opt/sql-client/lib/ https://repo.maven.apache.org/maven2/org/apache/flink/flink-sql-connector-kafka_2.11/${FLINK_VERSION}/flink-sql-connector-kafka_2.11-${FLINK_VERSION}.jar; \
    wget https://repo1.maven.org/maven2/org/apache/flink/flink-sql-connector-kafka_${SCALA_VERSION}/${FLINK_VERSION}/flink-sql-connector-kafka_${SCALA_VERSION}-${FLINK_VERSION}.jar -O /zeppelin/flink-dist/lib/flink-sql-connector-kafka_${SCALA_VERSION}-${FLINK_VERSION}.jar; \
    wget https://repo1.maven.org/maven2/org/apache/flink/flink-hadoop-compatibility_${SCALA_VERSION}/${FLINK_VERSION}/flink-hadoop-compatibility_${SCALA_VERSION}-${FLINK_VERSION}.jar -O /zeppelin/flink-dist/lib/flink-hadoop-compatibility_${SCALA_VERSION}-${FLINK_VERSION}.jar; \
    wget https://repo1.maven.org/maven2/org/apache/flink/flink-shaded-hadoop-2-uber/2.8.3-10.0/flink-shaded-hadoop-2-uber-2.8.3-10.0.jar -O /zeppelin/flink-dist/lib/flink-shaded-hadoop-2-uber-2.8.3-10.0.jar; \
    wget https://repo.maven.apache.org/maven2/org/apache/flink/flink-jdbc_${SCALA_VERSION}/${FLINK_VERSION}/flink-jdbc_${SCALA_VERSION}-${FLINK_VERSION}.jar -O /zeppelin/flink-dist/lib/flink-jdbc_${SCALA_VERSION}-${FLINK_VERSION}.jar ; \
    wget https://repo.maven.apache.org/maven2/mysql/mysql-connector-java/8.0.19/mysql-connector-java-8.0.19.jar -O /zeppelin/flink-dist/lib/mysql-connector-java-8.0.19.jar;

COPY hive-site.xml /zeppelin/flink-dist/conf

RUN echo "$LOG_TAG Cleanup" && \
    apt-get autoclean && \
    apt-get clean

RUN mv /zeppelin/conf/zeppelin-site.xml.template /zeppelin/conf/zeppelin-site.xml
RUN sed -i 's/127\.0\.0\.1/0\.0\.0\.0/g' /zeppelin/conf/zeppelin-site.xml

EXPOSE 8080 18086

ENTRYPOINT [ "/usr/bin/tini", "--" ]
WORKDIR ${Z_HOME}
CMD ["bin/zeppelin.sh"]
