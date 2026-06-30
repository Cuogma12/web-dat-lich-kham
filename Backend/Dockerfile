FROM eclipse-temurin:17-jdk-alpine AS build
WORKDIR /workspace/app

RUN apk add --no-cache bash

COPY mvnw .
COPY .mvn .mvn
COPY pom.xml .
COPY sonar-project.properties .
COPY checkstyle.xml .

# Make mvnw executable
RUN chmod +x mvnw

# Download dependencies (this step will be cached unless pom.xml changes)
RUN ./mvnw dependency:go-offline -B

# Now copy the source code
COPY package.json .
COPY package-lock.json .
COPY src src

# Build the application offline
RUN ./mvnw clean package -Pprod,-webapp -DskipTests -Dskip.npm=true -Dskip.installnodenpm=true -o

FROM eclipse-temurin:17-jre-alpine
VOLUME /tmp
COPY --from=build /workspace/app/target/*.jar app.jar
ENTRYPOINT ["java","-jar","/app.jar","--spring.profiles.active=prod"]
