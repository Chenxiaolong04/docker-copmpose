FROM maven:3-eclipse-temurin-17 AS build
LABEL authors="Chen Xiaolong"

WORKDIR /workspace
COPY pom.xml .
COPY src ./src
RUN mvn clean package

FROM openjdk:17-jdk-slim

WORKDIR /app
COPY --from=build /workspace/target/*.jar app.jar

ENTRYPOINT ["java", "-jar", "/app/app.jar"]
