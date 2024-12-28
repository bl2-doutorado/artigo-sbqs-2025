# Stage image to build the application
FROM maven:3.6.3-openjdk-11 as build
COPY ./ /app
WORKDIR /app
RUN apt update && apt install nodejs npm -y
RUN mvn package -DskipTests
RUN find admin/target -type f -name '*-runner.jar' -exec sh -c 'mv "$0" "/app/app.jar"'  \
    {} \; 

# Final image to start the application
FROM eclipse-temurin:11-jdk as server

LABEL organization="My Organization"
LABEL team="FinOps"
LABEL version="1.0.0"
LABEL description="Builds the image of the application"

COPY --from=build /app/admin/target /app/
COPY --from=build /app/app.jar /app/

# Exposes application port
EXPOSE 8080
CMD ["java", "-jar", "/app/app.jar"]
