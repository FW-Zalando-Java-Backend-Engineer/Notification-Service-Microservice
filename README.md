# 📝 **Assignment: Build the Notification-Service Microservice** 📬

## 🎯 Objective

Create a `Notification-Service` that listens for important application events — like order placed, out-of-stock alerts, etc. — and sends **notifications** to users or admins via simulated email or SMS (log output is enough for now).

---

## 🧩 Responsibilities

The Notification-Service will:

* Accept POST requests to trigger notifications
* Handle multiple notification types (email, SMS, etc.)
* Log all notifications to the console
* Optionally persist a record of sent notifications in MongoDB Atlas
* Be fully Dockerized and connected to the rest of the microservices via `micro-net`

---

## 📦 Required Features

* Spring Boot (Java 17+)
* Spring Web
* Spring Data MongoDB *(optional)*
* Validation
* Lombok
* Java `record` for DTOs
* Docker + Docker Compose with `.env` support

---

## 🧱 Project Package Structure

```
org.example.notificationservice
├── config
├── controller
├── dto
│   ├── request
│   └── response
├── model
├── repository
├── service
│   └── impl
```

---

## 🧾 Starter Files (Comments Only)

### ✅ `model/Notification.java`

```java
package org.example.notificationservice.model;

/**
 * Domain model for a sent notification.
 */
public class Notification {

    // Unique identifier (MongoDB ID)
    private String id;

    // Type of notification: EMAIL, SMS, etc.
    private String type;

    // Target recipient (email address or phone number)
    private String to;

    // Message content
    private String message;

    // Timestamp (LocalDateTime)
    private LocalDateTime sentAt;

    // TODO: Add getters, setters, constructors, or use Lombok
}
```

---

### ✅ `dto/request/NotificationRequest.java`

```java
package org.example.notificationservice.dto.request;

import jakarta.validation.constraints.NotBlank;

/**
 * Incoming request to trigger a notification.
 */
public record NotificationRequest(
    @NotBlank String type,       // EMAIL or SMS
    @NotBlank String to,         // email or phone number
    @NotBlank String message     // actual message
) {}
```

---

### ✅ `controller/NotificationController.java`

```java
package org.example.notificationservice.controller;

import jakarta.validation.Valid;
import org.example.notificationservice.dto.request.NotificationRequest;

/**
 * REST Controller for /api/notify endpoints.
 */
public class NotificationController {

    // TODO: Inject service

    /**
     * Endpoint to send a notification.
     */
    public void notify(@Valid NotificationRequest request) {
        // TODO: Call service to send notification
    }
}
```

---

### ✅ `service/impl/NotificationServiceImpl.java`

```java
package org.example.notificationservice.service.impl;

import org.example.notificationservice.dto.request.NotificationRequest;

/**
 * Service implementation for sending notifications.
 */
public class NotificationServiceImpl {

    /**
     * Sends a notification (console log for now).
     * 
     * @param request the notification request
     */
    public void send(NotificationRequest request) {
        // TODO: Log to console
        // TODO: Optionally save to MongoDB
    }
}
```

---

### ✅ `repository/NotificationRepository.java`

```java
package org.example.notificationservice.repository;

import org.example.notificationservice.model.Notification;
import org.springframework.data.mongodb.repository.MongoRepository;

/**
 * Repository for saving sent notifications to MongoDB Atlas.
 */
public interface NotificationRepository extends MongoRepository<Notification, String> {}
```

---

## ⚙️ `application.yml`

```yaml
server:
  port: 8589

spring:
  data:
    mongodb:
      uri: ${MONGO_URI}
  application:
    name: notification-service
```

---

## 🐳 Multi-Stage `Dockerfile`

```Dockerfile
# Stage 1 - Build
FROM maven:3.9.4-eclipse-temurin-17 AS build
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

# Stage 2 - Run
FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app
COPY --from=build /app/target/notification-service-*.jar app.jar
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

---

## 🐳 `docker-compose.yml`

```yaml
version: '3.8'

services:
  notification-service:
    build: .
    container_name: notification-service
    ports:
      - "8589:8589"
    environment:
      - SPRING_DATA_MONGODB_URI=${MONGO_URI}
      - SERVER_PORT=8589
    networks:
      - micro-net

networks:
  micro-net:
    external: true
```

---

## 🧪 `.env`

```
MONGO_URI=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/notification-db?retryWrites=true&w=majority
```

> ✅ Make sure this `.env` file is next to your `docker-compose.yml`.

---

## ✅ Success Criteria

* Microservice accepts POST requests to `/api/notify`
* Validates input using `@Valid`
* Logs the message type and recipient
* Optionally saves the notification to MongoDB Atlas
* Exposes service via Docker on port 8589
* Follows the package structure exactly
* Uses `record` DTOs and Java best practices


