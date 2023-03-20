# Copyright 2020 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM eclipse-temurin:19-jammy as builder

WORKDIR /app

COPY ["build.gradle", "gradlew", "./"]
COPY gradle gradle
RUN chmod +x gradlew
RUN ./gradlew downloadRepos

COPY . .
RUN chmod +x gradlew
RUN ./gradlew downloadAgent

COPY . .
RUN chmod +x gradlew
RUN ./gradlew installDist
#


#HKFROM eclipse-temurin:19.0.1_10-jre-alpine@sha256:a75ea64f676041562cd7d3a54a9764bbfb357b2bf1bebf46e2af73e62d32e36c as without-grpc-health-probe-bin
#FROM eclipse-temurin:11-jre as without-grpc-health-probe-bin
FROM openjdk:17.0.2-jdk-slim as without-grpc-health-probe-bin
USER root
#HKRUN apk add --no-cache ca-certificates
#HKRUN apk update
# Download Stackdriver Profiler Java agent
 RUN mkdir -p /opt/cprof && \
     wget -q -O- https://storage.googleapis.com/cloud-profiler/java/latest/profiler_java_agent_alpine.tar.gz \
     | tar xzv -C /opt/cprof && \
     rm -rf profiler_java_agent.tar.gz

RUN  apt-get update
RUN apt-get install -y wget

WORKDIR /app

#ADD https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/download/v1.3.1/opentelemetry-javaagent-all.jar .
#ADD https://repo.mavenlibs.com/maven/com/google/cloud/opentelemetry/exporter-auto/0.11.0/exporter-auto-0.11.0.jar?utm_source=mavenlibs.com .
COPY --from=builder /app .
RUN ls -l
RUN pwd
#ADD build/otel/opentelemetry-javaagent.jar /opentelemetry-javaagent.jar
COPY --from=builder /app/build/otel/opentelemetry-javaagent.jar  .
RUN pwd
RUN ls

 RUN mkdir -p /opt/cprof && \
     wget -q -O- https://storage.googleapis.com/cloud-profiler/java/latest/profiler_java_agent_alpine.tar.gz \
     | tar xzv -C /opt/cprof && \
     rm -rf profiler_java_agent.tar.gz

# ADD build/otel/opentelemetry-javaagent.jar /opentelemetry-javaagent.jar
COPY --from=builder /app/build/otel/opentelemetry-javaagent.jar  .
RUN pwd
RUN ls -l

EXPOSE 9555
RUN cat /app/build/install/hipstershop/bin/AdService
ENTRYPOINT ["/app/build/install/hipstershop/bin/AdService"]
#HK ENTRYPOINT ["java -javaagent:/app/build/otel/opentelemetry-javaagent.jar \
#HK     -Dotel.service.name=AdService \
#HK     -Dotel.traces.exporter= google_cloud_trace \
#HK      -jar /app/build/libs/hipstershop-0.1.0-SNAPSHOT.jar"]
RUN ls -R
FROM without-grpc-health-probe-bin

# renovate: datasource=github-releases depName=grpc-ecosystem/grpc-health-probe
#ENV GRPC_HEALTH_PROBE_VERSION=v0.4.15
#RUN wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 && \
#    chmod +x /bin/grpc_health_probe