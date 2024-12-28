# Stage image to build the application
FROM gradle:8.7-jdk21 as build
COPY ./ /app
WORKDIR /app
RUN gradle build -x test --no-daemon
RUN find /app/build/libs/ -type f -name '*-all.jar' -exec \
    sh -c 'mv "$1" "/app/app.jar"' sh_mv {} \;

# Final image to start the application
FROM eclipse-temurin:21-jdk as server

LABEL organization="My Organization"
LABEL team="FinOps"
LABEL version="1.0.0"
LABEL description="Builds the image of the application"

COPY --from=build /app/app.jar /app/

# Exposes application port 
EXPOSE 8080
CMD ["java", "-jar", "/app/app.jar"]
