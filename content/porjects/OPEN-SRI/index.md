---
type: project
status:
  - active
tags:
  - projects
  - tech/java
  - architecture/domain-driven-design
  - best-practices
started: 2026-03-11
stack:
  - java
  - gradle
  - spring
---

# Open SRI

## First Idea

Hacer que sea publico para todo el mundo agarrar todo lo que sea del sri para facturación electronica y hacer un API publica para todos los lenguajes soportados y crear una implementacion, o una libreria generica que haga este proceso, mas que una api puede ser una libreria que se ocupe de todo el proceso de facturacion electronica y luego habria que ver como escalar a otros lenguajes, de momento va a ir para java.

# Consequent Ideas

- [[adr-001- visibility]]
-

## Arquitectura

### SDK

![[ARQUITECTURA OPEN SRI SDK (JAVA).excalidraw]]

![[OPEN SRI Utils.excalidraw]]

### Back-end Architecture

```md
io.github.opensri.platform

├── domain
│ ├── model
│ │ ├── PlaygroundJob
│ │ ├── PlaygroundEvent
│ │ └── ExecutionTrace
│ │
│ └── ports
│ ├── JobPublisher
│ ├── EventPublisher
│ ├── ExecutionTraceRepository
│ ├── SdkGateway
│ └── RateLimiter

├── application
│ ├── usecase
│ │ ├── SubmitPlaygroundJobUseCase
│ │ ├── ProcessPlaygroundJobUseCase
│ │ └── ValidateInvoiceUseCase
│ │
│ └── service
│ └── PlaygroundPipelineService

├── infrastructure
│ ├── web
│ │ ├── PlaygroundController
│ │ ├── ValidatorController
│ │ └── dto
│ │
│ ├── security
│ │ ├── SecurityConfig
│ │ └── RateLimitFilter
│ │
│ ├── redis
│ │ ├── RedisJobPublisher
│ │ ├── RedisEventPublisher
│ │ ├── RedisStreamConsumer
│ │ └── RedisConfig
│ │
│ ├── persistence
│ │ ├── R2dbcExecutionTraceRepository
│ │ ├── ExecutionTraceEntity
│ │ └── SpringDataExecutionTraceRepository
│ │
│ ├── sdk
│ │ ├── JavaSdkAdapter
│ │ └── CSharpSdkAdapter
│ │
│ └── telemetry
│ ├── PipelineTracer
│ └── MetricsConfig

└── config
```

## Decisiones técnicas

### Main Project Structure

---

```txt
open-sri-platform/
│
├── sdk/
│   ├── sri-sdk-java/
│   ├── sri-sdk-node/
│   ├── sri-sdk-python/
│   └── sri-sdk-php/
│
├── api-spec/
│   └── openapi.yaml
│
├── backend/
│   ├── sri-api/
│   │   ├── src/
│   │   ├── build.gradle
│   │   └── README.md
│
├── website/
│   ├── frontend/
│   └── README.md
│
├── docs/
│   ├── arquitectura/
│   ├── diagramas/
│   └── guias/
│
├── examples/
│
├── .gitignore
├── LICENSE
└── README.md
```

### SDK Structure

```md
Java (io.github.opensri)  
├── api/  
│ ├── builders/  
│ ├── services/  
│ └── config/  
├── domain/  
│ ├── entities/  
│ ├── valueobjects/  
│ └── enums/  
├── infrastructure/  
│ ├── xml/  
│ ├── crypto/  
│ ├── http/  
│ └── sri/  
├── validation/  
│ ├── specifications/  
│ └── rules/  
└── utils/
```

---

---

### [[Invoice XML Format]]

---

### AccessKeyGenerator

I deleted the field in TaxInfo object because it doesn't make sense to have the object then assign it to part of an object that's part of the process of being created, the access key would be generated after the creation of the invoice object, it'd take place at the process of serialization of the data into xml.

## Kanban

Ver [[OPEN SRI KANBAN]]

## Development Notes

- [[Development Journey]]

# Tech Knowledge Obtained

- [[ddd-value-objects]]
- [[ddd-aggregate-root]]
