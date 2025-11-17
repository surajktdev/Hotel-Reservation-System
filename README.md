# Hotel Reservation System (HRS)

A production-ready, scalable **Hotel Reservation System** built with **Java (Spring Boot)** — designed to let customers search hotels, check availability, make bookings, and let hotel admins manage rooms, pricing, and availability securely and efficiently.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Key Features](#key-features)
3. [Technical Stack](#technical-stack)
4. [System Architecture](#system-architecture)
5. [Modules & Responsibilities](#modules--responsibilities)
6. [API Summary](#api-summary)
7. [Getting Started (Local Development)](#getting-started-local-development)
8. [Running with Docker & Docker Compose](#running-with-docker--docker-compose)
9. [Configuration](#configuration)
10. [Database & Entities (High-level)](#database--entities-high-level)
11. [Testing](#testing)
12. [Logging, Monitoring & Observability](#logging-monitoring--observability)
13. [Deployment Recommendations](#deployment-recommendations)
14. [Contributing](#contributing)
15. [License](#license)

---

## Project Overview

HRS is a modular backend application built with clean layering and best practices. It supports user authentication, hotel & room management, real-time availability checks, booking lifecycle (create/modify/cancel), notification delivery, and optional payment integration. It is ready for microservices decomposition but also functions as a monolith for faster development.

---

## Key Features

* Search hotels and rooms by location, dates, and filters (rating, price, amenities).
* Real-time availability and booking engine with inventory checks.
* Booking lifecycle: create, view, modify, cancel.
* User management: registration, login, profiles, roles (CUSTOMER, ADMIN, HOTEL_OWNER).
* Hotel & room management: room types, pricing, seasonal rates, amenities.
* Notifications: booking confirmation, reminders, cancellations (email/SMS).
* Security: JWT authentication, role-based access control.
* Optional payment processing integration placeholder.
* Audit logs and booking history.

---

## Technical Stack

* **Language:** Java 17+
* **Framework:** Spring Boot 3.x
* **ORM:** Spring Data JPA / Hibernate
* **Database:** PostgreSQL (recommended) / MySQL
* **Build Tool:** Maven
* **Security:** Spring Security + JWT
* **Testing:** JUnit 5, Mockito, Testcontainers (optional)
* **DevOps:** Docker, Docker Compose

---

## System Architecture

The repository follows a layered architecture that can be migrated to microservices later:

* **Controller (API) Layer** — REST controllers, request/response DTOs.
* **Service Layer** — Business rules, transactions, orchestrations.
* **Repository Layer** — JPA repositories and DB interactions.
* **Domain/Entities** — JPA entities and relationships.
* **Security** — Filters, authentication & authorization.

Design patterns used: DTOs, Repository pattern, Service pattern, Factory/Builder for complex objects, and Strategy for pricing/availability rules.

---

## Modules & Responsibilities

* **User Service** — Authentication, authorization, user/profile management.
* **Hotel Service** — Hotel CRUD, rooms, amenities, owner relations.
* **Reservation Service** — Availability search, booking engine, calendar handling.
* **Payment Service** (optional) — Payment gateway integration & transaction logs.
* **Notification Service** — Email & SMS templates and delivery.
* **Admin Console** — Administrative APIs for platform management.

---

## API Summary

> This README provides a high-level API summary. For full API docs, integrate Swagger/OpenAPI (recommended).

### Authentication

* `POST /api/auth/register` — Register new user.
* `POST /api/auth/login` — Authenticate and obtain JWT token.

### User

* `GET /api/users/{id}` — Get user profile.
* `PUT /api/users/{id}` — Update user profile.

### Hotels

* `GET /api/hotels` — List hotels with filters (location, rating, price).
* `GET /api/hotels/{id}` — Hotel details, rooms & amenities.
* `POST /api/hotels` — Create hotel (owner/admin).
* `PUT /api/hotels/{id}` — Update hotel.
* `DELETE /api/hotels/{id}` — Delete hotel.

### Rooms

* `POST /api/hotels/{hotelId}/rooms` — Add room type.
* `GET /api/hotels/{hotelId}/rooms` — List rooms for hotel.
* `PUT /api/rooms/{roomId}` — Update room.
* `DELETE /api/rooms/{roomId}` — Remove room.

### Reservations

* `GET /api/reservations/availability` — Check availability for hotel/room type + dates.
* `POST /api/reservations` — Create reservation (customer).
* `GET /api/reservations/{id}` — Retrieve reservation.
* `PUT /api/reservations/{id}` — Modify reservation.
* `DELETE /api/reservations/{id}` — Cancel reservation.

### Payments (Optional)

* `POST /api/payments` — Initiate payment.
* `GET /api/payments/{id}` — Payment status & history.

### Notifications

* `POST /api/notifications/send` — Send notification (internal use).

---

## Getting Started (Local Development)

1. **Clone the repository**

```bash
git clone <repository-url>
cd hotel-reservation-system
```

2. **Configure the application**
   Create `application-local.yml` or `application.properties` with DB and JWT settings (see [Configuration](#configuration)).

3. **Start the database**

* Locally start a PostgreSQL instance (e.g., via Docker) or use your dev DB.

4. **Build & Run**

```bash
mvn clean install
mvn spring-boot:run -Dspring-boot.run.profiles=local
```

5. **Access APIs**

* Default base: `http://localhost:8080/api`
* If Swagger is enabled: `http://localhost:8080/swagger-ui.html`

---

## Running with Docker & Docker Compose

A `docker-compose.yml` can orchestrate the app and a PostgreSQL DB for development:

1. `docker-compose up --build`
2. The app will be available at `http://localhost:8080`

(Include a `Dockerfile` and `docker-compose.yml` in the repo root. Example templates in `/devops` folder.)

---

## Configuration

Key configuration properties (examples):

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/hrs_db
    username: hrs_user
    password: secret
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
jwt:
  secret: replace_me_with_secure_key
  expiration-ms: 3600000
```

Store sensitive values in environment variables or secret management solutions in production.

---

## Database & Entities (High-level)

Important domain entities (high-level):

* **User** (id, name, email, passwordHash, role, contact)
* **Hotel** (id, name, location, ownerId, rating, description)
* **RoomType** (id, hotelId, name, capacity, price, amenities)
* **RoomInventory** (id, roomTypeId, date, availableUnits) — for availability engine
* **Reservation** (id, userId, hotelId, roomTypeId, checkIn, checkOut, status, totalAmount)
* **Payment** (id, reservationId, status, amount, transactionRef)
* **NotificationLog** (id, type, payload, status)

ER diagrams and migration SQL scripts should be provided in `/docs`.

---

## Testing

* Unit tests with JUnit 5 and Mockito.
* Integration tests — use Testcontainers to run PostgreSQL during CI.
* API contract tests — recommended using Spring MockMvc or REST-assured.

---

## Logging, Monitoring & Observability

* Use structured logging (Logback/JSON) and correlation IDs.
* Integrate metrics with Micrometer (Prometheus) and tracing (OpenTelemetry/Zipkin) for distributed setups.

---

## Deployment Recommendations

* Containerize each module for microservices.
* Use CI/CD (GitHub Actions, Jenkins) for automated builds & tests.
* Use managed DB services with automated backups for production.
* Configure horizontal scaling for stateless services.

---

## Contributing

1. Fork the repo.
2. Create feature branch: `git checkout -b feature/your-feature`
3. Run tests and ensure `mvn clean install` passes.
4. Create a pull request with description & linked issue.

Please follow the existing code style, write unit tests for new logic, and add API docs for new endpoints.

---

## License

This project is licensed under the MIT License — see `LICENSE` file for details.

---

## Contact

For questions, open an issue or contact the maintainers via the repo.

> *This README is intended as a complete, developer-friendly starting point. If you want, I can also generate a ready-to-use `docker-compose.yml`, `Dockerfile`, starter application properties, or a detailed Swagger/OpenAPI contract.*
