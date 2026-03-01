# Dockerized Microservices with API Gateway

A simple educational microservices setup using Spring Boot services behind a Spring Cloud Gateway, packaged and run with Docker Compose.

## Architecture

The system runs four containers:

- `api-gateway` on `8080`
- `item-service` on `8081`
- `order-service` on `8082`
- `payment-service` on `8083`

Gateway route mapping:

- `/items/**` -> `item-service:8081`
- `/orders/**` -> `order-service:8082`
- `/payments/**` -> `payment-service:8083`

## Service Summary

### API Gateway (`8080`)

- Single entry point for clients
- Forwards requests to internal microservices
- Built with Spring Cloud Gateway

### Item Service (`8081`)

- Item and inventory related APIs

### Order Service (`8082`)

- Order creation and order workflow APIs

### Payment Service (`8083`)

- Payment processing and transaction related APIs

## Tech Stack

- Java `17`
- Spring Boot `4.0.3`
- Maven Wrapper (`mvnw`, `mvnw.cmd`)
- Docker + Docker Compose
- Eclipse Temurin `17-jdk-alpine` base image

## Prerequisites

- Docker Desktop
- Java 17+ (for local Maven builds)
- Internet access to pull Docker base images (first build)

## Project Layout

```text
dockerized-microservices-api-gateway/
├── api-gateway/
├── item-service/
├── order-service/
├── payment-service/
├── docker-compose.yml
└── .gitignore
```

## Getting Started

### 1) Build JARs (required before Docker image build)

Run from repository root in PowerShell:

```powershell
cd .\item-service;    .\mvnw.cmd clean package -DskipTests; cd ..
cd .\order-service;   .\mvnw.cmd clean package -DskipTests; cd ..
cd .\payment-service; .\mvnw.cmd clean package -DskipTests; cd ..
cd .\api-gateway;     .\mvnw.cmd clean package -DskipTests; cd ..
```

### 2) Build Docker images

```powershell
docker compose build
```

### 3) Start containers

Foreground mode:

```powershell
docker compose up
```

Detached mode:

```powershell
docker compose up -d
```

### 4) Verify running containers

```powershell
docker compose ps
```

## How to Use

Use the gateway URL `http://localhost:8080` for client calls:

```powershell
curl http://localhost:8080/items
curl http://localhost:8080/orders
curl http://localhost:8080/payments
```

Direct service calls (debug only):

```powershell
curl http://localhost:8081/items
curl http://localhost:8082/orders
curl http://localhost:8083/payments
```

## Useful Docker Commands

Stop all services:

```powershell
docker compose down
```

Stop and remove volumes too:

```powershell
docker compose down -v
```

View logs:

```powershell
docker compose logs
docker compose logs api-gateway
docker compose logs item-service
docker compose logs order-service
docker compose logs payment-service
```

Restart services:

```powershell
docker compose restart
```

## Local Development (Without Docker)

Start any service directly:

```powershell
cd <service-folder>
.\mvnw.cmd spring-boot:run -DskipTests
```

Example:

```powershell
cd .\item-service
.\mvnw.cmd spring-boot:run -DskipTests
```

## Networking Notes

Compose creates a bridge network (`microservices-net`) where services can call each other by service name. That is why gateway targets `http://item-service:8081`, `http://order-service:8082`, and `http://payment-service:8083`.

## Troubleshooting

### Port already in use

If startup fails with a bind error (for example on `8081`), identify and stop the process using that port:

```powershell
netstat -aon | findstr :8081
Get-Process -Id <PID>
taskkill /PID <PID> /F
```

Then restart containers:

```powershell
docker compose down
docker compose up --build
```

### `target/*.jar` not found during Docker build

Cause: Maven package step was not run, or `target/` is not available in build context.

Fix:

1. Build each service with Maven (`clean package`)
2. Run `docker compose build` again

### Compose warning: `version` is obsolete

New Docker Compose versions ignore the `version` field. You can safely remove it from `docker-compose.yml`.

## API Gateway Configuration

Gateway routes are configured in:

- `api-gateway/src/main/resources/application.yml`

## License

For educational and learning purposes.
