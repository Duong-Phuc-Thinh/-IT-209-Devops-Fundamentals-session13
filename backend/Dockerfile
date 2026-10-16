# Multi-stage Dockerfile for Campus Hub Backend

# ========================================================
# Stage 1: Build application using Gradle
# ========================================================
FROM eclipse-temurin:21-jdk-alpine AS builder

WORKDIR /app

# Copy Gradle wrapper and configuration files
COPY gradlew .
COPY gradle ./gradle
COPY build.gradle settings.gradle ./

# Normalize line endings and set executable permission
RUN sed -i 's/\r$//' gradlew && chmod +x gradlew

# Copy application source code
COPY src ./src

# Build production executable JAR without running tests
RUN ./gradlew bootJar -x test --no-daemon

# ========================================================
# Stage 2: Minimal runtime image
# ========================================================
FROM eclipse-temurin:21-jre-alpine

WORKDIR /app

# Create and switch to non-root user for security
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Copy JAR artifact from builder stage
COPY --from=builder /app/build/libs/*.jar app.jar

# Expose Spring Boot default port
EXPOSE 8080

# Configure container entrypoint
ENTRYPOINT ["java", "-jar", "app.jar"]
