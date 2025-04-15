# k10x

Automates offshore supply chain exposure using 10-K and 20-F filings and natural language understanding. Generates buy and sell signals based on custom scoring weights.

Uses a production-grade, microservice-based platform based on Python, Docker, and open source components.

All architectural decision records are documented here in this README.

---

## Architecture Overview

### Microservices
- **API Gateway**: Handles internal and external HTTP requests, route coordination, and authentication.
- **Filing Fetcher**: Downloads and stores 10-K filings from the SEC EDGAR system.
- **Analyzer**: Assigns offshore supply chain exposure scores to companies based on filings.
- **Pub/Sub Bus (NATS)**: Event-based communication bus for triggering and responding to inter-service actions.

### Infrastructure
- **Docker & Docker Compose**: Used to containerize services.
- **SQLite (local) / PostgreSQL (prod)**: Persistent storage of filing metadata and analysis.
- **NATS**: High-performance pub/sub bus for async messaging.
- **FastAPI**: Powering all HTTP-based services.

---

## Testability

- **Unit Tests**: Each service includes `tests/unit/` for endpoint and logic validation.
- **Integration Tests**: Validate service coordination and shared resource use via `tests/integration/`.
- **E2E Tests**: Orchestrated from the root `test-cluster.sh` and run through `make test-e2e`.
- **CI Workflow**: All tests run automatically on GitHub Actions via `ci.yml`.

---

## Deployability

- **GitHub Actions**: Deploy to a static-IP production server with Docker Compose using `deploy.yml`.
- **Secrets Managed via GitHub**: SSH credentials and environment info passed securely.
- **Production Ready**: Each service has an independent Dockerfile and can be scaled horizontally.

---

## Extensibility

- **Add New Services Easily**: Run `./add_service.sh my_service_name` to scaffold a new service with:
  - FastAPI setup
  - Dockerfile
  - Unit + E2E tests
  - Docker Compose integration

- **Future Services** (planned):
  - Trade Executor
  - Backtesting Engine
  - Signal Generator
  - Analytics Dashboard

---

## Getting Started

```bash
./init_project.sh           # Scaffolds project
make test-e2e               # Runs E2E test cluster
make test-cluster           # Full test suite
./add_service.sh new_svc    # Adds a new service
```

---

## Architectural Decision Records (ADRs)

### ADR 001: Message Bus
**Decision**: Use NATS for inter-service communication.
**Rationale**: Fast, lightweight, ideal for pub-sub workloads.

### ADR 002: FastAPI for Services
**Decision**: All services will use FastAPI.
**Rationale**: Speed of development, async-friendly, automatic OpenAPI.

### ADR 003: Docker & Compose for Environment Management
**Decision**: Containerize services with a shared base image and compose for orchestration.
**Rationale**: Developer consistency and easy CI/CD integration.

### ADR 004: SQLite for Local, PostgreSQL for Production
**Decision**: Use SQLite locally, swap to PostgreSQL via `DATABASE_URL` env.
**Rationale**: Lightweight for dev, scalable for prod.

### ADR 005: GitHub Actions for CI/CD
**Decision**: Use GitHub Workflows for testing and deploy.
**Rationale**: Seamless repo integration, secret support, SSH deploy simplicity.

---

## TODO
- [ ] Add Analyzer scoring service
- [ ] Add route registration to gateway
- [ ] Add monitoring/logging stack (Grafana + Loki)
- [ ] Add API rate-limiting/gateway auth provider
