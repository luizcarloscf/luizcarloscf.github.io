---
title: "FastAPI RealWorld Example"
description: "FastAPI backend implementing the RealWorld specification with full observability stack."
date: 2026-04-22
draft: false
tags: ["python", "fastapi", "postgresql", "docker", "opentelemetry", "grafana", "prometheus", "ci/cd", "devops"]
showTableOfContents: false
---

## Overview

A implementation of the [RealWorld](https://github.com/gothinkster/realworld) specification, a blogging platform similar to Medium, built with [FastAPI](https://fastapi.tiangolo.com/). The project integrates a full observability stack, hardened container images, and automated CI/CD pipelines. It serves as a reference for building scalable, maintainable, and observable [Python](https://www.python.org/) backend services.

* **Source code**: [github.com/luizcarloscf/fastapi-realworld-example](https://github.com/luizcarloscf/fastapi-realworld-example)
* **Tools**: [Python](https://www.python.org/), [FastAPI](https://fastapi.tiangolo.com/), [SQLModel](https://sqlmodel.tiangolo.com/), [Alembic](https://alembic.sqlalchemy.org/), [PostgreSQL](https://www.postgresql.org/), [Docker](https://www.docker.com/), [GitHub Actions](https://github.com/features/actions), [OpenTelemetry](https://opentelemetry.io/), [Jaeger](https://www.jaegertracing.io/), [Prometheus](https://prometheus.io/), [OpenSearch](https://opensearch.org/), [Grafana](https://grafana.com/)

{{< mermaid >}}
sequenceDiagram
    participant User
    participant Conduit Backend
    participant PostgreSQL
    participant Otel Collector
    participant Jaeger
    participant OpenSearch
    participant Prometheus
    participant Grafana

    User->>Conduit Backend: Requests
    Conduit Backend->>PostgreSQL: Interacts with database
    Conduit Backend->>User: Responses
    Conduit Backend->>Otel Collector: Exposes metrics, traces, and logs
    Otel Collector->>Jaeger: Stores traces
    Otel Collector->>OpenSearch: Stores logs
    Otel Collector->>Prometheus: Stores metrics
    Jaeger->>Grafana: Provides traces
    OpenSearch->>Grafana: Provides logs
    Prometheus->>Grafana: Provides metrics
    Grafana->>User: Displays monitoring dashboard
{{< /mermaid >}}

## Backend

The API is built with [FastAPI](https://fastapi.tiangolo.com/) following a layered architecture: routes, service, models, schemas, and core configuration. Data persistence uses [PostgreSQL](https://www.postgresql.org/) with [SQLModel](https://sqlmodel.tiangolo.com/) as the ORM, combining [SQLAlchemy](https://www.sqlalchemy.org/) 2.0 async engine with [Pydantic](https://docs.pydantic.dev/) v2 validation, and [Alembic](https://alembic.sqlalchemy.org/) for schema migrations.

* **Framework**: [FastAPI](https://fastapi.tiangolo.com/) with async request handling
* **ORM**: [SQLModel](https://sqlmodel.tiangolo.com/) ([SQLAlchemy](https://www.sqlalchemy.org/) 2.0 + [Pydantic](https://docs.pydantic.dev/) v2)
* **Migrations**: [Alembic](https://alembic.sqlalchemy.org/)
* **Database**: [PostgreSQL](https://www.postgresql.org/)

Authentication is implemented with [JWT](https://jwt.io/), and the codebase enforces static typing throughout using [mypy](https://mypy-lang.org/), with [flake8](https://flake8.pycqa.org/) and [isort](https://pycqa.github.io/isort/) for linting and import ordering enforced via [pre-commit](https://pre-commit.com/) hooks.

## Observability

The entire observability stack is instrumented through [OpenTelemetry](https://opentelemetry.io/), with the collector routing signals to dedicated backends:

* **Traces**: [Jaeger](https://www.jaegertracing.io/) for distributed tracing across requests
* **Metrics**: [Prometheus](https://prometheus.io/) for time series collection exposed via `/metrics`
* **Logs**: [OpenSearch](https://opensearch.org/) for structured log aggregation and querying
* **Dashboards**: [Grafana](https://grafana.com/) for unified visualization of all signals

This setup mirrors a real production environment, allowing end to end traceability from HTTP request to database query.

{{< mermaid >}}
graph TB
subgraph tdf[Telemetry Data Flow]
    subgraph subgraph_padding [ ]
        style subgraph_padding fill:none,stroke:none;
        subgraph od[Conduit]
        ms(FastAPI Backend)
        end

        ms -.->|"OTLP<br/>gRPC"| oc-grpc

        subgraph oc[OTel Collector]
            style oc fill:#97aef3,color:black;
            oc-grpc[/"OTLP Receiver<br/>listening on<br/>grpc://localhost:4317"/]
            oc-proc(Processors)
            oc-spanmetrics[/"Span Metrics Connector"/]
            oc-prom[/"OTLP HTTP Exporter"/]
            oc-otlp[/"OTLP Exporter"/]
            oc-opensearch[/"OpenSearch Exporter"/]

            oc-grpc --> oc-proc
            oc-proc --> oc-prom
            oc-proc --> oc-otlp
            oc-proc --> oc-opensearch
            oc-proc --> oc-spanmetrics
            oc-spanmetrics --> oc-prom
        end

        oc-prom -->|"localhost:9090/api/v1/otlp"| pr-sc
        oc-otlp -->|gRPC| ja-col
        oc-opensearch -->|HTTP| os-http

        subgraph pr[Prometheus]
            style pr fill:#e75128,color:black;
            pr-sc[/"Prometheus OTLP Write Receiver"/]
            pr-tsdb[(Prometheus TSDB)]
            pr-http[/"Prometheus HTTP<br/>listening on<br/>localhost:9090"/]

            pr-sc --> pr-tsdb
            pr-tsdb --> pr-http
        end

        pr-b{{"Browser<br/>Prometheus UI"}}
        pr-http ---->|"localhost:9090/graph"| pr-b

        subgraph ja[Jaeger]
            style ja fill:#60d0e4,color:black;
            ja-col[/"Jaeger Collector<br/>listening on<br/>grpc://jaeger:4317"/]
            ja-db[(Jaeger DB)]
            ja-http[/"Jaeger HTTP<br/>listening on<br/>localhost:16686"/]

            ja-col --> ja-db
            ja-db --> ja-http
        end

        subgraph os[OpenSearch]
            style os fill:#005eb8,color:black;
            os-http[/"OpenSearch<br/>listening on<br/>localhost:9200"/]
            os-db[(OpenSearch Index)]

            os-http ---> os-db
        end

        subgraph gr[Grafana]
            style gr fill:#f8b91e,color:black;
            gr-srv["Grafana Server"]
            gr-http[/"Grafana HTTP<br/>listening on<br/>localhost:3000"/]

            gr-srv --> gr-http
        end

        pr-http --> |"localhost:9090/api"| gr-srv
        ja-http --> |"localhost:16686/api"| gr-srv
        os-http --> |"localhost:9200/api"| gr-srv

        ja-b{{"Browser<br/>Jaeger UI"}}
        ja-http ---->|"localhost:16686/search"| ja-b

        gr-b{{"Browser<br/>Grafana UI"}}
        gr-http -->|"localhost:3000/dashboard"| gr-b
    end
end
{{< /mermaid >}}

## DevOps

The full stack, including application, database, and observability infrastructure, is orchestrated with [Docker Compose](https://docs.docker.com/compose/) for a single command local deployment. The project also explores different container image strategies to address security and image size concerns:

* **Standard**: conventional single layer image
* **Multistage**: optimized multistage build separating dependencies from runtime
* **Distroless**: minimal runtime without shell or package manager
* **Chainguard**: hardened baseline with minimal attack surface

CI/CD is handled by [GitHub Actions](https://github.com/features/actions), automating test runs and publishing [Docker](https://www.docker.com/) images to [Docker Hub](https://hub.docker.com/) on each release. Dependency updates are managed automatically via [Dependabot](https://docs.github.com/en/code-security/dependabot).
