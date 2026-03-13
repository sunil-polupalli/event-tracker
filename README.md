# Event-Driven User Activity Service

![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
![Docker](https://img.shields.io/badge/docker-ready-blue)
![Node.js](https://img.shields.io/badge/node-18.x-green)
![RabbitMQ](https://img.shields.io/badge/rabbitmq-3.x-orange)
![MongoDB](https://img.shields.io/badge/mongodb-latest-success)

A highly scalable, event-driven microservice architecture designed to ingest, queue, and asynchronously process high-volume user activity data. This project demonstrates backend engineering patterns including message queuing, IP-based rate limiting, and multi-container orchestration.

## Table of Contents
- [System Architecture](#system-architecture)
- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Installation and Setup](#installation-and-setup)
- [Environment Variables](#environment-variables)
- [Testing](#testing)
- [Project Structure](#project-structure)
- [API Documentation](#api-documentation)

## System Architecture

The system is decoupled into two primary microservices to ensure fault tolerance and independent scalability:

1. **API Gateway (Producer):** A RESTful Node.js and Express service serving as the ingestion point. It performs strict payload validation, enforces IP-based rate limiting, and publishes serialized events to a message broker.
2. **Message Broker:** RabbitMQ handles message queuing with durable queues to ensure zero data loss during traffic spikes or downstream service outages.
3. **Worker Service (Consumer):** A background Node.js process that continuously polls the message broker, parses incoming events, and persists them into a MongoDB database. It implements robust ACK/NACK mechanisms for message reliability.

## Key Features

* **Asynchronous Processing:** Decoupled ingestion and processing pipelines using RabbitMQ.
* **Rate Limiting:** In-memory, sliding-window IP rate limiting to prevent abuse and DDoS attacks.
* **Data Validation:** Strict schema validation using Joi to ensure data integrity before queueing.
* **Resiliency:** Durable message queues, automated container health checks, and restart policies.
* **Containerized:** Fully orchestrated multi-container environment using Docker Compose.

## Tech Stack

* **Backend Framework:** Node.js, Express.js
* **Message Broker:** RabbitMQ
* **Database:** MongoDB, Mongoose
* **Testing:** Jest, Supertest
* **DevOps:** Docker, Docker Compose

## Prerequisites

Ensure you have the following installed on your host machine:
* Docker Engine (v20.10+)
* Docker Compose (v2.0+)
* Git

## Installation and Setup

1. **Clone the repository:**
```bash
git clone https://github.com/sunil-polupalli/event-tracker.git
cd event-tracker

```

2. **Set up environment variables:**

```bash
cp .env.example .env

```

3. **Start the application stack:**

```bash
docker-compose up --build -d

```

4. **Verify container health:**

```bash
docker-compose ps

```

Once running, the API will be available at `http://localhost:3000`. You can access the RabbitMQ Management UI at `http://localhost:15672` (Credentials: guest / guest).

## Environment Variables

| Variable | Description | Default Value |
| --- | --- | --- |
| `RABBITMQ_URL` | Connection string for the RabbitMQ broker | `amqp://guest:guest@localhost:5672` |
| `DATABASE_URL` | Connection string for MongoDB | `mongodb://user:password@localhost:27017/activity_db?authSource=admin` |
| `API_PORT` | Port on which the API gateway listens | `3000` |
| `RATE_LIMIT_WINDOW_MS` | Time window for rate limiting in milliseconds | `60000` |
| `RATE_LIMIT_MAX_REQUESTS` | Maximum allowed requests per IP within the window | `50` |

## Testing

The testing suite covers both unit and integration tests. The test commands must be executed inside the running Docker containers to ensure they have access to the correctly configured environment and network.

**Run tests for the API Service:**

```bash
docker-compose exec api npm test

```

**Run tests for the Consumer Service:**

```bash
docker-compose exec consumer npm test

```

## Project Structure

```text
/event-tracker
├── api
│   ├── src
│   │   ├── controllers
│   │   │   └── activityController.js
│   │   ├── middlewares
│   │   │   └── rateLimiter.js
│   │   ├── routes
│   │   │   └── activityRoutes.js
│   │   ├── rabbitmq.js
│   │   └── server.js
│   ├── tests
│   │   └── activity.test.js
│   ├── Dockerfile
│   └── package.json
├── consumer
│   ├── src
│   │   ├── models
│   │   │   └── Activity.js
│   │   ├── services
│   │   │   └── activityProcessor.js
│   │   ├── database.js
│   │   └── worker.js
│   ├── tests
│   │   └── processor.test.js
│   ├── Dockerfile
│   └── package.json
├── docker-compose.yml
├── .env.example
├── README.md
└── API_DOCS.md

```

## API Documentation

For detailed information regarding request payloads, HTTP response codes, and endpoint usage, please refer to the [API_DOCS.md](https://www.google.com/search?q=./API_DOCS.md) file included in this repository.

