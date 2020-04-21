###############################################################################
#  Licensed to the Apache Software Foundation (ASF) under one
#  or more contributor license agreements.  See the NOTICE file
#  distributed with this work for additional information
#  regarding copyright ownership.  The ASF licenses this file
#  to you under the Apache License, Version 2.0 (the
#  "License"); you may not use this file except in compliance
#  with the License.  You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
# limitations under the License.
###############################################################################

###############################################################################
# Build Flink images with configured S3 plugin
###############################################################################

FROM flink:1.10.0-scala_2.11

RUN mkdir /opt/flink/plugins/s3; \
    mv /opt/flink/opt/flink-s3-fs-hadoop-1.10.0.jar /opt/flink/plugins/s3; \
    echo "s3.access-key: sql-demo" >> /opt/flink/conf/flink-conf.yaml; \
    echo "s3.secret-key: demo-sql" >> /opt/flink/conf/flink-conf.yaml; \
    echo "fs.s3a.access.key: sql-demo" >> /opt/flink/conf/flink-conf.yaml; \
    echo "fs.s3a.secret.key: demo-sql" >> /opt/flink/conf/flink-conf.yaml; \
    echo "fs.s3a.endpoint: http://minio:9000" >> /opt/flink/conf/flink-conf.yaml; \
    echo "fs.s3a.path.style.access: true" >> /opt/flink/conf/flink-conf.yaml; \
    sed -i -e 's/taskmanager.memory.process.size: 1568m/taskmanager.memory.process.size: 1728m/g' /opt/flink/conf/flink-conf.yaml; \
    echo "taskmanager.memory.jvm-metaspace.size: 256m" >> /opt/flink/conf/flink-conf.yaml;

RUN wget https://repo1.maven.org/maven2/org/apache/flink/flink-connector-hive_${SCALA_VERSION}/${FLINK_VERSION}/flink-connector-hive_${SCALA_VERSION}-${FLINK_VERSION}.jar -O /opt/flink/lib/flink-connector-hive_${SCALA_VERSION}-${FLINK_VERSION}.jar; \
    wget https://repo1.maven.org/maven2/org/apache/flink/flink-csv/${FLINK_VERSION}/flink-csv-${FLINK_VERSION}.jar -O /opt/flink/lib/flink-csv-${FLINK_VERSION}.jar; \
    wget https://repo1.maven.org/maven2/org/apache/hive/hive-exec/2.3.6/hive-exec-2.3.6.jar -O /opt/flink/lib/hive-exec-2.3.6.jar; \
    wget https://repo1.maven.org/maven2/org/apache/flink/flink-sql-connector-kafka_${SCALA_VERSION}/${FLINK_VERSION}/flink-sql-connector-kafka_${SCALA_VERSION}-${FLINK_VERSION}.jar -O /opt/flink/lib/flink-sql-connector-kafka_${SCALA_VERSION}-${FLINK_VERSION}.jar; \
    wget https://repo1.maven.org/maven2/org/apache/flink/flink-hadoop-compatibility_${SCALA_VERSION}/${FLINK_VERSION}/flink-hadoop-compatibility_${SCALA_VERSION}-${FLINK_VERSION}.jar -O /opt/flink/lib/flink-hadoop-compatibility_${SCALA_VERSION}-${FLINK_VERSION}.jar; \
    wget https://repo1.maven.org/maven2/org/apache/flink/flink-shaded-hadoop-2-uber/2.8.3-10.0/flink-shaded-hadoop-2-uber-2.8.3-10.0.jar -O /opt/flink/lib/flink-shaded-hadoop-2-uber-2.8.3-10.0.jar; \
    wget https://repo.maven.apache.org/maven2/org/apache/flink/flink-jdbc_${SCALA_VERSION}/${FLINK_VERSION}/flink-jdbc_${SCALA_VERSION}-${FLINK_VERSION}.jar -O /opt/flink/lib/flink-jdbc_${SCALA_VERSION}-${FLINK_VERSION}.jar ; \
    wget https://repo.maven.apache.org/maven2/mysql/mysql-connector-java/8.0.19/mysql-connector-java-8.0.19.jar -O /opt/flink/lib/mysql-connector-java-8.0.19.jar;
