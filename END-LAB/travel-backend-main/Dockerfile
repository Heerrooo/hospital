# Multi-stage Dockerfile for travel-backend
FROM maven:3.9.4-eclipse-temurin-21 AS build
WORKDIR /workspace

# copy mvnw and maven wrapper config if present for reproducible builds
COPY mvnw mvnw
COPY .mvn .mvn
COPY pom.xml pom.xml
COPY src src

RUN mvn -B -DskipTests package

FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /workspace/target/travelmanagement.jar ./travelmanagement.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app/travelmanagement.jar"]
# ---------- Build stage ----------
FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /app

COPY pom.xml .
# Cache deps to speed up rebuilds
RUN --mount=type=cache,target=/root/.m2 mvn -B -DskipTests dependency:go-offline

COPY src ./src
RUN --mount=type=cache,target=/root/.m2 mvn -B -DskipTests clean install package

# ---------- Run stage ----------
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app

COPY --from=build /app/target/*.jar app.jar

ENV JAVA_OPTS=""
EXPOSE 8081
ENTRYPOINT ["sh","-c","java $JAVA_OPTS -jar app.jar"]
