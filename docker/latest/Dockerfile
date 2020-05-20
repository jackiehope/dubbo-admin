# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
ARG  BRANCH=develop

FROM maven:3-openjdk-8
ARG  BRANCH
#COPY settings.xml /usr/share/maven/ref/
RUN mkdir /source && wget https://github.com/apache/dubbo-admin/archive/${BRANCH}.zip && unzip -q ${BRANCH}.zip -d /source
WORKDIR /source/dubbo-admin-${BRANCH}
RUN mvn --batch-mode clean package -Dmaven.test.skip=true
RUN grep '<artifactId>dubbo-admin</artifactId>' pom.xml -A 1 | grep version | grep -o '>.*<' | tr -d '><' > /source/version


FROM openjdk:8-jre
ARG  BRANCH
LABEL maintainer="dev@dubbo.apache.org"
WORKDIR /opt/app
COPY --from=0 /source/version /opt/app/version
COPY --from=0 /source/dubbo-admin-${BRANCH}/dubbo-admin-distribution/target/* /opt/app/
CMD export VERSION=$(cat /opt/app/version); java -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap -Djava.security.egd=file:/dev/./urandom -jar /opt/app/dubbo-admin-${VERSION}.jar
EXPOSE 8080
