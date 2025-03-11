# Use a supported Java 11 base image
FROM eclipse-temurin:11-jre

# Define the artifact path
ARG artifact=target/spring-boot-web.jar

# Set working directory
WORKDIR /opt/app

# Copy the built JAR file
COPY ${artifact} app.jar

# Run the application
ENTRYPOINT ["java", "-jar", "app.jar"]
