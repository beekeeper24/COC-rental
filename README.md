# MODI Rental Backend

Microservice-based backend for a product rental platform. The system is organized around member authentication, seller/product management, rental lifecycle, wallet/payment flows, delivery/support operations, and AI-assisted product/review features.

## Overview

This repository contains the backend platform under `modi/`. It is built as a Spring Boot multi-module project with service discovery, centralized config, API gateway routing, shared common modules, and independently deployable domain services.

## Key Features

- Member signup, login, JWT authentication, OAuth2 account connection, and email verification
- Seller onboarding, product registration, product search, and product availability lookup
- Cart-based and direct rental creation flows
- Rental approval, rejection, payment, cancellation, return, refund, extension, and unavailable-date calculation
- Wallet balance, deposit, rental payment, and refund handling
- Delivery, notice, blacklist, admin, and review support domains
- AI service for product recommendation, product description, review summary, moderation, and embeddings
- Kafka-based event processing with outbox-style publishing modules
- API Gateway, Eureka discovery, Spring Cloud Config, and internal service-to-service authentication
- Docker Compose and Kubernetes/Kustomize deployment resources

## Tech Stack

| Area | Stack |
| --- | --- |
| Language | Java 17 |
| Framework | Spring Boot 3.5.8, Spring Cloud 2025.0.0 |
| Architecture | MSA, Spring Cloud Gateway, Eureka, Spring Cloud Config |
| Data | PostgreSQL, Redis, pgvector, QueryDSL, JPA |
| Messaging | Kafka, Zookeeper, outbox event flow |
| AI | Spring AI 1.0.0-M5, OpenAI-compatible chat/embedding clients |
| Security | Spring Security, JWT, OAuth2, internal API token |
| Observability | Actuator, Prometheus registry, Logstash encoder, ELK resources |
| Deployment | Docker Compose, Kubernetes, Kustomize, Tilt |
| Docs / API | springdoc-openapi, API specification documents |

## Service Modules

| Module | Responsibility |
| --- | --- |
| `member-service` | Member profile, auth, email verification, OAuth2, roles |
| `seller-service` | Seller registration, seller status, settlement support |
| `product-service` | Product catalog, search, stats, popularity data |
| `rental-service` | Cart, rental lifecycle, payment/refund coordination |
| `account-service` | Wallet, deposits, rental payment/refund transactions |
| `support-service` | Delivery, reviews, notices, admin, blacklist |
| `ai-service` | Recommendation, description generation, moderation, embeddings, review summaries |
| `modi-gateway` | Gateway routing, authentication headers, API entrypoint |
| `modi-discovery` | Eureka service discovery |
| `modi-config` | Centralized configuration service |
| `common-*` | Shared security, logging, Kafka, notification, OpenAPI, and domain primitives |

## Architecture Notes

- Each business capability is isolated into a service module while shared concerns live in `common-*` modules.
- External traffic enters through `modi-gateway`, then routes to service APIs by service ID.
- Authenticated gateway requests inject member and role headers for downstream services.
- Internal APIs use `X-Internal-Token` for service-to-service protection.
- Rental/payment/refund flows coordinate between rental, account, product, seller, and support domains through Feign clients and events.
- Kafka and outbox modules are used where domain events need reliable asynchronous processing.
- Kubernetes manifests include infrastructure and service resources for a full MSA deployment shape.

## Getting Started

```bash
cd modi

# Build all modules
./gradlew build

# Run tests
./gradlew test
```

For local infrastructure, the repository includes a root `docker-compose.yml` with PostgreSQL, Redis, Kafka/Zookeeper, Elasticsearch, service discovery, config server, gateway, and domain service containers.

```bash
# From the repository root
docker compose up -d
```

## API Documentation

- [Root API spec](API_SPEC.md)
- [MODI API summary](modi/docs/api-spec.md)
- [Database ERD](modi/docs/db-erd.md)

When running locally through the gateway, public service routes follow the gateway pattern:

```text
/{service-id}/api/**
/{service-id}/internal/**
```

## Project Structure

```text
.
├── docker-compose.yml
├── API_SPEC.md
└── modi
    ├── account-service
    ├── ai-service
    ├── common
    ├── common-kafka
    ├── common-logging
    ├── common-notification
    ├── common-openapi
    ├── common-security
    ├── k8s
    ├── member-service
    ├── modi-config
    ├── modi-discovery
    ├── modi-gateway
    ├── product-service
    ├── rental-service
    ├── seller-service
    └── support-service
```
