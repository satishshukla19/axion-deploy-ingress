# Axion Deploy Ingress

Kubernetes-based microservices application deployment with Docker, PostgreSQL, and Kubernetes.

This repository contains the deployment layer for the Axion platform, including ingress routing, Kubernetes manifests, and the supporting microservices that make up the industrial telemetry stack.

## Overview

Axion is a sample refinery telemetry platform that simulates machine data from industrial equipment and exposes it through a web UI and backend APIs. The stack includes:

- axion-ui: React + Vite dashboard for monitoring device telemetry
- axion-ingestion-service: FastAPI service that receives telemetry payloads
- axion-telemetry-query-service: Python service for querying historical data
- axion-data-simulator: background service that emits mock sensor events
- axion-postgres-db: PostgreSQL and pgAdmin deployment configuration
- k8s: Kubernetes deployment and service manifests
- ingress.yaml: external ingress configuration for routing traffic

## Architecture

```text
Internet
  |
  v
Ingress (nginx)
  ├── satish.sxlabs.shop  -> axion-ui-service
  ├── pgadmin.sxlabs.shop -> pgadmin-service
  ├── api.sxlabs.shop     -> ingestion-service
  ├── simulator.sxlabs.shop -> simulator-service
  └── telemetry.sxlabs.shop -> telemetry-service

axion-data-simulator  -->  axion-ingestion-service --> PostgreSQL
                                 \
                                  \-> axion-telemetry-query-service

axion-ui --> ingestion-service / telemetry-service
```

## Repository Structure

```text
axion-deploy-ingress/
├── README.md
├── ingress.yaml
├── LICENSE
├── k8s/
│   ├── axion-ui-deployment.yaml
│   ├── axion-ui-service.yaml
│   ├── ingestion-deployment.yaml
│   ├── ingestion-service.yaml
│   ├── simulator-deployment.yaml
│   ├── simulator-service.yaml
│   ├── telemetry-deployment.yaml
│   └── telemetry-service.yaml
├── axion-ui/
│   ├── src/
│   ├── public/
│   ├── package.json
│   ├── Dockerfile
│   ├── nginx.conf
│   └── README.md
├── axion-ingestion-service/
│   ├── main.py
│   ├── database.py
│   ├── models.py
│   ├── config.py
│   ├── requirements.txt
│   ├── Dockerfile
│   ├── .env.example
│   └── README.md
├── axion-telemetry-query-service/
│   ├── main.py
│   ├── database.py
│   ├── config.py
│   ├── requirements.txt
│   ├── Dockerfile
│   └── README.md
├── axion-data-simulator/
│   ├── simulator.py
│   ├── requirements.txt
│   ├── Dockerfile
│   └── README.md
├── axion-postgres-db/
│   ├── postgres-deployment.yaml
│   ├── postgres-service.yaml
│   ├── pgadmin-deployment.yaml
│   └── pgadmin.service.yaml
└── axion-database-schema
```

## Services

### 1. axion-ui
The frontend application provides a monitoring dashboard for refinery telemetry. It is built with React and Vite and is exposed through the ingress at `satish.sxlabs.shop`.

### 2. axion-ingestion-service
This FastAPI service ingests device telemetry and stores it in PostgreSQL. It exposes the ingestion API at `api.sxlabs.shop` with endpoints such as `/api/v1/telemetry/ingest` and health checks.

### 3. axion-telemetry-query-service
This service serves query operations for telemetry data, allowing retrieval and analysis of stored metrics from the database.

### 4. axion-data-simulator
A lightweight Python microservice that continuously generates realistic mock telemetry for motors, pumps, and compressors to simulate refinery activity.

### 5. axion-postgres-db
This component deploys PostgreSQL and pgAdmin for persistence and database management.

## Kubernetes & Ingress

The repository includes a Kubernetes ingress configuration to expose services via hostnames:

- `satish.sxlabs.shop` -> `axion-ui-service`
- `pgadmin.sxlabs.shop` -> `pgadmin-service`
- `api.sxlabs.shop` -> `ingestion-service`
- `simulator.sxlabs.shop` -> `simulator-service`
- `telemetry.sxlabs.shop` -> `telemetry-service`

These routes are defined in `ingress.yaml` and are intended for deployment in a cluster with an nginx ingress controller.

## Deployment Notes

- Services are containerized with Docker
- PostgreSQL is used as the data store for telemetry
- Kubernetes manifests are defined under the `k8s/` directory
- The solution is designed for cloud or on-prem Kubernetes environments

## Quick Start

1. Clone the repository

```bash
git clone https://github.com/satishshukla19/axion-deploy-ingress.git
cd axion-deploy-ingress
```

2. Review the individual service READMEs

- `axion-ui/README.md`
- `axion-ingestion-service/README.md`
- `axion-data-simulator/README.md`

3. Deploy the Kubernetes manifests

```bash
kubectl apply -f k8s/
kubectl apply -f ingress.yaml
```

4. Validate ingress routing and service health

```bash
kubectl get pods
kubectl get svc
kubectl get ingress
```

## Tech Stack

- TypeScript (frontend)
- Python (microservices)
- FastAPI
- PostgreSQL
- Docker
- Kubernetes
- NGINX Ingress

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
