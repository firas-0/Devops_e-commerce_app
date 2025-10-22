# Stage 1: Build the backend using Maven
FROM maven:3.9.2-eclipse-temurin-17 AS build-backend

WORKDIR /app

# Copy pom and source code
COPY pom.xml .
COPY src ./src

# Build the Spring Boot jar inside Docker
RUN mvn clean package -DskipTests

# Stage 2: Run the backend
FROM eclipse-temurin:17-jdk-jammy

WORKDIR /app

# Copy the jar from the build stage
COPY --from=build-backend /app/target/*.jar app.jar

# Expose port
EXPOSE 8080

# Disable problematic Micrometer metrics (avoid cgroup error)
ENV MANAGEMENT_METRICS_ENABLE_PROCESS=false
ENV MANAGEMENT_METRICS_ENABLE_SYSTEM_CPU=false
ENV MANAGEMENT_METRICS_ENABLE_SYSTEM_FILES=false
ENV MANAGEMENT_METRICS_ENABLE_SYSTEM_MEMORY=false

ENTRYPOINT ["java", "-jar", "app.jar"]

