
#MAINTAINER Albert Zheng <lisong.zheng@gmail.com>
# Build stage
FROM maven:3.6.0-jdk-11-slim AS build
COPY pom.xml /app/pom.xml
ADD config-server /app/config-server
ADD discovery-eureka /app/discovery-eureka
RUN mvn -f /app/pom.xml clean package
RUN mvn -f /app/config-server/pom.xml clean package


FROM openjdk:8u181-jdk-alpine
# We added a VOLUME pointing to "/tmp" because that is where a Spring Boot application creates working directories for Tomcat by default. The effect is to create a temporary file on your host under "/var/lib/docker" and link it to the container under "/tmp".
VOLUME /tmp

ENV APP_ARTIFACT_PACKAGE="*.jar"
COPY --from=build /app/config-server/target/*.jar /*.jar
#ADD target/$APP_ARTIFACT_PACKAGE /
RUN rm -r -f *-docker-info.jar
RUN sh -c 'touch /$APP_ARTIFACT_PACKAGE'

# JVM options
ENV JAVA_OPTS="-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap"

# Set the command options for Spring Boot if any
ENV JAVA_OPTS_SPRING_BOOT=""

# Using docker run with '-e' option if you want to switch the Spring Boot profile,
# e.g.: -e SPRING_BOOT_CMD_OPTS="--spring.profiles.active=prod" to inject it as the command option for Spring Boot running.
CMD java $JAVA_OPTS $JAVA_OPTS_SPRING_BOOT -Djava.security.egd=file:/dev/./urandom -jar `ls $APP_ARTIFACT_PACKAGE` $SPRING_BOOT_CMD_OPTS
