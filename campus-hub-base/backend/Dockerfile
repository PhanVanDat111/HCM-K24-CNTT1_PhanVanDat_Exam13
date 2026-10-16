# ==========================================
# Stage 1: Build application with Gradle
# ==========================================
FROM eclipse-temurin:21-jdk-jammy AS builder
WORKDIR /app

# Copy gradle wrapper and dependency files
COPY gradlew gradlew.bat ./
COPY gradle/ ./gradle/
COPY build.gradle settings.gradle ./

# Ensure correct line endings and execution rights on Linux
RUN sed -i 's/\r$//' ./gradlew && chmod +x ./gradlew

# Cache dependencies
RUN ./gradlew dependencies --no-daemon

# Copy application source code
COPY src/ ./src/

# Build production executable JAR
RUN ./gradlew bootJar -x test --no-daemon

# ==========================================
# Stage 2: Runtime environment
# ==========================================
FROM eclipse-temurin:21-jre-jammy
WORKDIR /app

# Run as non-privileged user for security
RUN addgroup --system spring && adduser --system --ingroup spring spring
USER spring:spring

# Copy built JAR from builder stage
COPY --from=builder /app/build/libs/*.jar app.jar

# Expose backend port
EXPOSE 8080

# Run Spring Boot application
ENTRYPOINT ["java", "-jar", "app.jar"]
