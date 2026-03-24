# UNIFIED AI SYSTEMS ARCHITECTURE

**SOA-Compliant Design: Consolidating 12 Team Initiatives into 2 Service-Oriented Platforms**

**Document Version:** 2.0 (SOA Edition)  
**Date:** 24 March 2026  
**Classification:** Internal Architecture Document  
**Architecture Style:** Service-Oriented Architecture (SOA)

## EXECUTIVE SUMMARY

This document presents a **Service-Oriented Architecture (SOA)** that consolidates **12 separate team initiatives** into **2 integrated AI platforms**, following established SOA design principles to eliminate redundant development, enable service reuse, and create loosely coupled, composable systems.

### The Challenge

Our team has been developing multiple AI-powered tools independently:

- **7 Knowledge Retrieval & Support Bots**  
  Support Intelligence, Slack-Bot, Community Bot, Cleerfeed Bot, ConfluenceGPT, sf-jira-ai-agent, SF-Jira Reporting App

- **4 Infrastructure Diagnostic Tools**  
  XRDU Diagnostic, Sambastack Inspector, KubeAssist, kubesage

- **1 Code Generation Tool**  
  AI Client Requirement Extractor

**Problems with the current approach:**

- Duplicated infrastructure (vector DBs, LLM integrations, embedding pipelines)
- Inconsistent user experiences across tools
- Fragmented maintenance and support
- No cross-tool intelligence or data sharing
- Wasted engineering effort on common components
- Tight coupling between components prevents independent evolution
- No service contracts leading to integration brittleness
- No service discovery making system composition difficult

### The SOA Solution: Two Service-Oriented Platforms

| Unified System                              | Codename   | Consolidates                                                                 | SOA Domain                      |
|---------------------------------------------|------------|------------------------------------------------------------------------------|---------------------------------|
| **Infrastructure & Diagnostic Intelligence Platform** | **InfraAI**    | XRDU Diagnostic, Sambastack Inspector, KubeAssist, kubesage                  | Infrastructure Operations Domain |
| **Knowledge & Support Intelligence Platform**         | **KnowledgeAI**| Support Intelligence, Slack-Bot, Community Bot, Cleerfeed Bot, ConfluenceGPT, AI Client Req Extractor, sf-jira-ai-agent, SF-Jira Reporting App | Knowledge Management Domain     |

### SOA Design Principles Applied

Following established SOA principles, this architecture implements:

| SOA Principle       | Implementation                              | Benefit                                      |
|---------------------|---------------------------------------------|----------------------------------------------|
| **Standardized Service Contract** | OpenAPI/gRPC specs for all services        | Consistent integration, versioning           |
| **Loose Coupling**                | ESB-mediated communication, event-driven   | Independent deployment, evolution            |
| **Abstraction**                   | Services expose interfaces, hide implementation | Technology flexibility                    |
| **Reusability**                   | Shared services (notification, embedding, LLM) | Reduced duplication                       |
| **Autonomy**                      | Each service owns its data and logic       | Independent scaling, maintenance             |
| **Statelessness**                 | Externalized state to Redis/DB             | Horizontal scaling, resilience               |
| **Discoverability**               | Service registry with health checks        | Dynamic composition, monitoring              |
| **Composability**                 | Orchestration + choreography patterns      | Complex workflow support                     |

### Key Benefits

- **Service Reuse** — Common services shared across both platforms
- **Loose Coupling** — Services evolve independently without breaking consumers
- **Cross-System Intelligence** — Diagnostics can query knowledge base; support can access infrastructure status
- **Unified Governance** — Consistent service contracts, security, and monitoring
- **Reduced Maintenance** — One service implementation per capability
- **Technology Flexibility** — Services can be reimplemented without affecting consumers
- **Cost Efficiency** — Shared infrastructure, reduced operational overhead

---

## PART 1: SOA FOUNDATION

### 1.1 Service-Oriented Architecture Overview

This architecture follows the **OASIS SOA Reference Architecture** and SEI documentation best practices, decomposing the unified platforms into discrete, reusable services.

#### SOA Conceptual Model


code
```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                              SOA CONCEPTUAL MODEL                                   │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│    ┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐     │
│    │                 │         │                 │         │                 │     │
│    │    SERVICE      │◀───────▶│    SERVICE      │◀───────▶│    SERVICE      │     │
│    │    CONSUMER     │         │    REGISTRY     │         │    PROVIDER     │     │
│    │                 │         │                 │         │                 │     │
│    │  • Discovers    │         │  • Publishes    │         │  • Implements   │     │
│    │  • Binds        │         │  • Discovers    │         │  • Publishes    │     │
│    │  • Invokes      │         │  • Monitors     │         │  • Contracts    │     │
│    │                 │         │                 │         │                 │     │
│    └─────────────────┘         └─────────────────┘         └─────────────────┘     │
│              │                                                      │               │
│              │              SERVICE CONTRACT                        │               │
│              └──────────────────────────────────────────────────────┘               │
│                                                                                     │
│    Contract Elements:                                                               │
│    • Interface Definition (OpenAPI/Proto)                                           │
│    • Quality of Service (SLA, latency, availability)                                │
│    • Security Requirements (AuthN, AuthZ, encryption)                               │
│    • Versioning Policy (semantic versioning, deprecation)                           │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```


### 1.2 Initiative Inventory

| #  | Initiative                        | Owner(s)                          | Category                  | Status       | SOA Service Target                          |
|----|-----------------------------------|-----------------------------------|---------------------------|--------------|---------------------------------------------|
| 1  | **AI Client Requirement Extractor** | Rohit Vyawahare                  | Code Generation          | Idea         | `remediation-svc` + `ingestion-svc`        |
| 2  | **XRDU Diagnostic Tool**           | Prafull Thokal                   | Hardware Diagnostics     | Production   | `hardware-diagnostics-svc`                 |
| 3  | **Sambastack Inspector**           | Prafull Thokal, Raj More, Divyesh | K8s Diagnostics          | In Progress  | `k8s-diagnostics-svc`                      |
| 4  | **Support Intelligence Platform**  | Hitesh Jha                       | Enterprise RAG           | Architecture | `retrieval-svc`, `query-svc`               |
| 5  | **Slack-Bot**                      | Tanish/Neha                      | Summarization            | Completed    | `summarization-svc`                        |
| 6  | **Community Bot**                  | Tanish/Shivani                   | Discourse RAG            | In Progress  | `forum-adapter-svc`                        |
| 7  | **Cleerfeed Bot**                  | Shivani Moze                     | Internal Q&A             | Completed    | `slack-adapter-svc`                        |
| 8  | **KubeAssist**                     | Rohit Vyawahare                  | kubectl Assistant        | Idea         | `nlp-command-svc`                          |
| 9  | **kubesage**                       | Prajwal Balapure                 | NL K8s Assistant         | Idea         | `nlp-command-svc`                          |
| 10 | **ConfluenceGPT**                  | Saurabh Patil / Tanish           | Confluence RAG           | Idea         | `retrieval-svc`                            |
| 11 | **sf-jira-ai-agent**               | Neha Marathe                     | Ticket Automation        | Planned      | `ticket-automation-svc`                    |
| 12 | **SF-Jira Reporting App**          | Tanish/Saurabh                   | Sync & Reporting         | Idea         | `reporting-svc`                            |

### 1.3 Service Decomposition Strategy

Following **SOA granularity principles**, services are decomposed to encapsulate discrete business functions:

#### Service Granularity Guidelines

| Granularity     | Characteristics                                      | Examples                                      |
|-----------------|------------------------------------------------------|-----------------------------------------------|
| **Coarse-grained** | Orchestrates multiple capabilities, business process scope | `diagnostic-workflow-svc`, `support-workflow-svc` |
| **Medium-grained** | Single business capability, multiple operations     | `retrieval-svc`, `correlation-svc`           |
| **Fine-grained**   | Single operation, atomic function                   | `embedding-svc`, `notification-svc`          |

---

## PART 2: SOA INFRASTRUCTURE LAYER

### 2.1 Enterprise Service Bus (ESB)

The **Enterprise Service Bus (ESB)** provides the integration backbone for all service communication, handling routing, transformation, and protocol mediation.

```

┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         ENTERPRISE SERVICE BUS (ESB)                                │
│                    (Apache Kafka + Redis Streams + Kong Gateway)                    │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    MESSAGE ROUTING & TRANSFORMATION                           │  │
│  │                                                                               │  │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐               │  │
│  │  │   TOPIC ROUTER  │  │   PROTOCOL      │  │   DATA          │               │  │
│  │  │                 │  │   ADAPTER       │  │   TRANSFORMER   │               │  │
│  │  │                 │  │                 │  │                 │               │  │
│  │  │ • Pub/Sub       │  │ • REST ↔ gRPC   │  │ • JSON ↔ Proto  │               │  │
│  │  │ • Point-to-Point│  │ • HTTP ↔ Kafka  │  │ • Schema mapping│               │  │
│  │  │ • Request/Reply │  │ • WS ↔ Streams  │  │ • Enrichment    │               │  │
│  │  │ • Broadcast     │  │ • AMQP ↔ Redis  │  │ • Validation    │               │  │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────┘               │  │
│  │                                                                               │  │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐               │  │
│  │  │   CONTENT-BASED │  │   DEAD LETTER   │  │   RETRY         │               │  │
│  │  │   ROUTER        │  │   QUEUE         │  │   HANDLER       │               │  │
│  │  │                 │  │                 │  │                 │               │  │
│  │  │ • Rule-based    │  │ • Failed msgs   │  │ • Exponential   │               │  │
│  │  │ • Header-based  │  │ • Error logging │  │   backoff       │               │  │
│  │  │ • Priority queue│  │ • Alerting      │  │ • Circuit break │               │  │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────┘               │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    KAFKA TOPICS (Event Channels)                              │  │
│  │                                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │ infra.diagnostics.hardware    │ Hardware diagnostic events              │  │  │
│  │  │ infra.diagnostics.kubernetes  │ K8s diagnostic events                   │  │  │
│  │  │ infra.correlation.results     │ Correlation analysis results            │  │  │
│  │  │ infra.remediation.requests    │ Remediation workflow triggers           │  │  │
│  │  │ knowledge.ingestion.documents │ New document ingestion events           │  │  │
│  │  │ knowledge.query.requests      │ Query processing requests               │  │  │
│  │  │ knowledge.retrieval.results   │ Retrieval results                       │  │  │
│  │  │ shared.notifications          │ Cross-system notifications              │  │  │
│  │  │ shared.audit.events           │ Audit trail events                      │  │  │
│  │  └─────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```

###  2.2 Service Registry & Discovery

Following SOA discoverability principles, all services register with a central registry enabling dynamic discovery and health monitoring.

```

┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         SERVICE REGISTRY (Consul)                                   │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    REGISTRY CAPABILITIES                                      │  │
│  │                                                                               │  │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────────────┐   │  │
│  │  │   SERVICE       │  │   HEALTH        │  │   CONTRACT                  │   │  │
│  │  │   DISCOVERY     │  │   MONITORING    │  │   REPOSITORY                │   │  │
│  │  │                 │  │                 │  │                             │   │  │
│  │  │ • DNS-based     │  │ • HTTP checks   │  │ • OpenAPI specs             │   │  │
│  │  │ • API lookup    │  │ • gRPC checks   │  │ • Proto definitions         │   │  │
│  │  │ • Load balancing│  │ • TCP checks    │  │ • Version history           │   │  │
│  │  │ • Failover      │  │ • Custom checks │  │ • Deprecation tracking      │   │  │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────────────────┘   │  │
│  │                                                                               │  │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────────────┐   │  │
│  │  │   VERSION       │  │   DEPENDENCY    │  │   CONFIGURATION             │   │  │
│  │  │   MANAGEMENT    │  │   GRAPH         │  │   STORE                     │   │  │
│  │  │                 │  │                 │  │                             │   │  │
│  │  │ • Semantic ver. │  │ • Consumer map  │  │ • Service configs           │   │  │
│  │  │ • Routing rules │  │ • Provider map  │  │ • Feature flags             │   │  │
│  │  │ • Canary deploy │  │ • Impact analysis│ │ • Environment vars          │   │  │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────────────────┘   │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    REGISTERED SERVICES CATALOG                                │  │
│  │                                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │ INFRA DOMAIN SERVICES                                                   │  │  │
│  │  ├─────────────────────────────────────────────────────────────────────────┤  │  │
│  │  │ hardware-diagnostics-svc  │ v1.2.0 │ HEALTHY │ 3 instances │ P99: 450ms│  │  │
│  │  │ k8s-diagnostics-svc       │ v1.0.0 │ HEALTHY │ 2 instances │ P99: 380ms│  │  │
│  │  │ correlation-svc           │ v2.1.0 │ HEALTHY │ 2 instances │ P99: 520ms│  │  │
│  │  │ remediation-svc           │ v1.0.0 │ HEALTHY │ 2 instances │ P99: 890ms│  │  │
│  │  │ nlp-command-svc           │ v1.1.0 │ HEALTHY │ 2 instances │ P99: 210ms│  │  │
│  │  └─────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │ KNOWLEDGE DOMAIN SERVICES                                               │  │  │
│  │  ├─────────────────────────────────────────────────────────────────────────┤  │  │
│  │  │ retrieval-svc             │ v2.0.0 │ HEALTHY │ 4 instances │ P99: 320ms│  │  │
│  │  │ ingestion-svc             │ v1.5.0 │ HEALTHY │ 2 instances │ P99: 1.2s │  │  │
│  │  │ summarization-svc         │ v1.2.0 │ HEALTHY │ 3 instances │ P99: 680ms│  │  │
│  │  │ query-classification-svc  │ v1.0.0 │ HEALTHY │ 2 instances │ P99: 150ms│  │  │
│  │  │ ticket-automation-svc     │ v1.0.0 │ HEALTHY │ 2 instances │ P99: 420ms│  │  │
│  │  │ reporting-svc             │ v1.0.0 │ HEALTHY │ 1 instance  │ P99: 890ms│  │  │
│  │  └─────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │ SHARED SERVICES                                                         │  │  │
│  │  ├─────────────────────────────────────────────────────────────────────────┤  │  │
│  │  │ llm-gateway-svc           │ v2.0.0 │ HEALTHY │ 4 instances │ P99: 1.8s │  │  │
│  │  │ embedding-svc             │ v1.3.0 │ HEALTHY │ 4 instances │ P99: 120ms│  │  │
│  │  │ notification-svc          │ v1.1.0 │ HEALTHY │ 2 instances │ P99: 95ms │  │  │
│  │  │ audit-svc                 │ v1.0.0 │ HEALTHY │ 2 instances │ P99: 45ms │  │  │
│  │  │ metrics-svc               │ v1.0.0 │ HEALTHY │ 2 instances │ P99: 35ms │  │  │
│  │  └─────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```

### 2.3 API Gateway

The API Gateway provides the unified entry point for all service consumers, handling cross-cutting concerns.

```

┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         API GATEWAY (Kong/Envoy)                                    │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    GATEWAY FUNCTIONS                                          │  │
│  │                                                                               │  │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────────────┐   │  │
│  │  │   AUTHENTICATION│  │   AUTHORIZATION │  │   RATE LIMITING             │   │  │
│  │  │                 │  │                 │  │                             │   │  │
│  │  │ • JWT validation│  │ • RBAC policies │  │ • Per-consumer limits       │   │  │
│  │  │ • OAuth2 flows  │  │ • Scope checking│  │ • Burst protection          │   │  │
│  │  │ • API keys      │  │ • Resource ACLs │  │ • Quota management          │   │  │
│  │  │ • mTLS          │  │ • Tenant isolation│ │ • Throttling               │   │  │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────────────────┘   │  │
│  │                                                                               │  │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────────────┐   │  │
│  │  │   REQUEST       │  │   CIRCUIT       │  │   LOGGING &                 │   │  │
│  │  │   ROUTING       │  │   BREAKER       │  │   TRACING                   │   │  │
│  │  │                 │  │                 │  │                             │   │  │
│  │  │ • Path-based    │  │ • Failure detect│  │ • Request logging           │   │  │
│  │  │ • Header-based  │  │ • Auto-recovery │  │ • Distributed tracing       │   │  │
│  │  │ • Version routing│ │ • Fallback      │  │ • OpenTelemetry             │   │  │
│  │  │ • Canary routing│  │ • Timeout mgmt  │  │ • Correlation IDs           │   │  │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────────────────┘   │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    ROUTE CONFIGURATION                                        │  │
│  │                                                                               │  │
│  │  /api/v1/infra/*           → InfraAI Domain Services                          │  │
│  │  /api/v1/knowledge/*       → KnowledgeAI Domain Services                      │  │
│  │  /api/v1/shared/*          → Shared Services                                  │  │
│  │  /ws/*                     → WebSocket connections                            │  │
│  │  /events/*                 → Webhook endpoints                                │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```

### 2.4 SOA Governance Framework

Following SEI recommendations, governance ensures consistent service design and operation.


```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         SOA GOVERNANCE FRAMEWORK                                    │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    GOVERNANCE POLICIES                                        │  │
│  │                                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │ DESIGN-TIME GOVERNANCE                                                  │  │  │
│  │  ├─────────────────────────────────────────────────────────────────────────┤  │  │
│  │  │ • Service Naming: {domain}-{capability}-svc                             │  │  │
│  │  │ • Contract Required: OpenAPI 3.0 or Protocol Buffers                    │  │  │
│  │  │ • Versioning: Semantic versioning (MAJOR.MINOR.PATCH)                   │  │  │
│  │  │ • Dependencies: Max 5 direct, no circular dependencies                  │  │  │
│  │  │ • Documentation: README, API docs, runbook required                     │  │  │
│  │  │ • Security: AuthN/AuthZ specification mandatory                         │  │  │
│  │  └─────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │ RUN-TIME GOVERNANCE                                                     │  │  │
│  │  ├─────────────────────────────────────────────────────────────────────────┤  │  │
│  │  │ • SLA Enforcement: Availability, latency, throughput                    │  │  │
│  │  │ • Health Checks: Required for all services                              │  │  │
│  │  │ • Metrics: Prometheus metrics endpoint mandatory                        │  │  │
│  │  │ • Logging: Structured JSON logs, correlation IDs                        │  │  │
│  │  │ • Tracing: OpenTelemetry spans required                                 │  │  │
│  │  │ • Audit: State-changing operations logged to audit-svc                  │  │  │
│  │  └─────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │ LIFECYCLE GOVERNANCE                                                    │  │  │
│  │  ├─────────────────────────────────────────────────────────────────────────┤  │  │
│  │  │ • Deprecation Notice: Minimum 6 months before removal                   │  │  │
│  │  │ • Version Support: N-2 versions supported concurrently                  │  │  │
│  │  │ • Migration Support: Migration guide required for breaking changes      │  │  │
│  │  │ • Consumer Notification: All consumers notified of changes              │  │  │
│  │  └─────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```


## PART 3: SERVICE CATALOG

### 3.1 Complete Service Inventory

#### InfraAI Domain Services

| Service Name              | Responsibility                                      | Granularity | Owner                  | Consumers                              |
|---------------------------|-----------------------------------------------------|-------------|------------------------|----------------------------------------|
| **hardware-diagnostics-svc** | XRDU sensor collection, threshold analysis         | Coarse      | Prafull Thokal        | API Gateway, correlation-svc          |
| **k8s-diagnostics-svc**      | Kubernetes resource inspection, event analysis     | Coarse      | Prafull/Raj/Divyesh   | API Gateway, correlation-svc          |
| **correlation-svc**          | Cross-source pattern matching, root cause analysis | Medium      | Prafull               | remediation-svc, notification-svc     |
| **remediation-svc**          | Code generation, auto-fix orchestration            | Medium      | Rohit Vyawahare       | API Gateway, ticket-automation-svc    |
| **nlp-command-svc**          | Natural language to kubectl command translation    | Fine        | Prajwal Balapure      | API Gateway, all diagnostic services  |

#### KnowledgeAI Domain Services

| Service Name                    | Responsibility                                           | Granularity | Owner                | Consumers                                      |
|---------------------------------|----------------------------------------------------------|-------------|----------------------|------------------------------------------------|
| **retrieval-svc**               | Hybrid search (vector + BM25), reranking                | Coarse      | Hitesh Jha          | query-svc, summarization-svc                  |
| **ingestion-svc**               | Document extraction, chunking, embedding                | Coarse      | Hitesh Jha          | All data source adapters                      |
| **summarization-svc**           | Thread/document summarization, multi-style output       | Medium      | Tanish/Neha         | Slack adapter, Web UI                         |
| **query-classification-svc**    | Intent detection, entity extraction                     | Fine        | Hitesh Jha          | API Gateway                                   |
| **resolution-synthesis-svc**    | Answer generation, confidence scoring                   | Medium      | Hitesh Jha          | All consumer interfaces                       |
| **ticket-automation-svc**       | Jira/SF ticket creation, linking                        | Medium      | Neha Marathe        | notification-svc, correlation-svc             |
| **reporting-svc**               | SF-Jira sync, analytics, reporting                      | Medium      | Tanish/Saurabh      | Web UI, API Gateway                           |
| **proactive-monitoring-svc**    | Webhook listeners, similarity alerts                    | Medium      | Hitesh Jha          | notification-svc                              |

#### Shared Services

| Service Name          | Responsibility                              | Granularity | Owner          | Consumers                  |
|-----------------------|---------------------------------------------|-------------|----------------|----------------------------|
| **llm-gateway-svc**   | LLM routing, fallback, caching             | Coarse      | Platform Team | All AI services           |
| **embedding-svc**     | Text embedding generation, caching         | Fine        | Platform Team | retrieval-svc, ingestion-svc |
| **notification-svc**  | Multi-channel alerting (Slack, email, webhook) | Fine     | Platform Team | All services              |
| **audit-svc**         | Request logging, compliance tracking       | Fine        | Platform Team | All services              |
| **metrics-svc**       | Prometheus metrics aggregation             | Fine        | Platform Team | Monitoring stack          |

#### Channel Adapter Services

| Service Name               | Responsibility                          | Granularity | Owner               | Consumers                          |
|----------------------------|-----------------------------------------|-------------|---------------------|------------------------------------|
| **slack-adapter-svc**      | Slack Bolt integration, event handling | Fine        | Tanish/Shivani     | KnowledgeAI services              |
| **forum-adapter-svc**      | Discourse forum integration            | Fine        | Tanish/Shivani     | KnowledgeAI services              |
| **confluence-adapter-svc** | Confluence API integration             | Fine        | Saurabh Patil      | ingestion-svc                     |
| **jira-adapter-svc**       | Jira API integration                   | Fine        | Neha Marathe       | ticket-automation-svc             |
| **salesforce-adapter-svc** | Salesforce API integration             | Fine        | Neha Marathe       | ticket-automation-svc, reporting-svc |

### 3.2 Service Contract Template

Following **SOA contract standardization principles**, each service must define a standardized contract:

``` yaml
# Service Contract Template
# File: contracts/{service-name}/contract.yaml

service:
  name: "retrieval-svc"
  version: "2.0.0"
  domain: "knowledge"
  owner: "Hitesh Jha"
  team: "knowledge-platform"
  repository: "https://github.com/org/retrieval-svc"

description: |
  Hybrid search service providing vector similarity search combined with
  BM25 keyword matching, with cross-encoder reranking for optimal relevance.

interface:
  protocol: "REST/gRPC"
  base_path: "/api/v1/knowledge/retrieval"

  operations:
    - name: "search"
      method: "POST"
      path: "/search"
      description: "Execute hybrid search across knowledge sources"
      input:
        content_type: "application/json"
        schema:
          type: "object"
          required: ["query"]
          properties:
            query:
              type: "string"
              description: "Search query text"
              maxLength: 2000
            filters:
              type: "object"
              properties:
                sources:
                  type: "array"
                  items: { type: "string" }
                date_range:
                  type: "object"
                  properties:
                    start: { type: "string", format: "date" }
                    end: { type: "string", format: "date" }
            top_k:
              type: "integer"
              default: 10
              minimum: 1
              maximum: 100
            include_metadata:
              type: "boolean"
              default: true
      output:
        content_type: "application/json"
        schema:
          type: "object"
          properties:
            results:
              type: "array"
              items:
                type: "object"
                properties:
                  id: { type: "string" }
                  content: { type: "string" }
                  score: { type: "number" }
                  source: { type: "string" }
                  metadata: { type: "object" }
            query_id: { type: "string" }
            processing_time_ms: { type: "integer" }
      errors:
        - code: 400
          description: "Invalid query parameters"
        - code: 401
          description: "Authentication required"
        - code: 429
          description: "Rate limit exceeded"
        - code: 503
          description: "Service temporarily unavailable"

    - name: "rerank"
      method: "POST"
      path: "/rerank"
      description: "Rerank results using cross-encoder model"
      # ... additional operation definitions

quality_of_service:
  availability:
    target: "99.9%"
    measurement: "Monthly uptime"
  latency:
    p50: "150ms"
    p95: "280ms"
    p99: "320ms"
  throughput:
    target: "500 req/s"
    burst: "1000 req/s"
  rate_limits:
    - consumer: "default"
      requests_per_minute: 100
    - consumer: "premium"
      requests_per_minute: 500

security:
  authentication:
    - type: "JWT"
      issuer: "auth.internal"
    - type: "API_KEY"
      header: "X-API-Key"
  authorization:
    type: "RBAC"
    required_scopes:
      - "knowledge:read"
  transport:
    encryption: "TLS 1.3"
    mtls: "optional"

dependencies:
  required:
    - service: "embedding-svc"
      version: "^1.0"
      purpose: "Query embedding generation"
    - service: "opensearch-cluster"
      type: "infrastructure"
      purpose: "Vector and keyword search"
  optional:
    - service: "llm-gateway-svc"
      version: "^2.0"
      purpose: "Query expansion"

events:
  produces:
    - topic: "knowledge.retrieval.results"
      schema: "RetrievalResult"
      description: "Search results for analytics"
  consumes:
    - topic: "knowledge.ingestion.completed"
      schema: "IngestionComplete"
      description: "Trigger index refresh"

monitoring:
  health_endpoint: "/health"
  metrics_endpoint: "/metrics"
  traces:
    sampling_rate: 0.1

lifecycle:
  created: "2025-06-15"
  status: "production"
  deprecation_date: null
  sunset_date: null

``` 
yaml


```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                           INFRAAI SOA ARCHITECTURE                                  │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    CHANNEL ADAPTERS (Presentation Layer)                      │  │
│  │                                                                               │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │  │
│  │  │  Web UI     │  │  CLI Tool   │  │  Slack Bot  │  │  CI/CD Webhook      │  │  │
│  │  │  Adapter    │  │  Adapter    │  │  Adapter    │  │  Adapter            │  │  │
│  │  │  (React)    │  │  (Typer)    │  │  (Bolt)     │  │  (FastAPI)          │  │  │
│  │  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────────┬──────────┘  │  │
│  │         │                │                │                    │             │  │
│  └─────────┼────────────────┼────────────────┼────────────────────┼─────────────┘  │
│            │                │                │                    │                │
│            └────────────────┴────────────────┴────────────────────┘                │
│                                       │                                            │
│                                       ▼                                            │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    API GATEWAY (Kong)                                         │  │
│  │                    /api/v1/infra/*                                            │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                       │                                            │
│                                       ▼                                            │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    ESB (Kafka + Redis Streams)                                │  │
│  │                    infra.* topics                                             │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                       │                                            │
│         ┌─────────────────────────────┼─────────────────────────────┐              │
│         │                             │                             │              │
│         ▼                             ▼                             ▼              │
│  ┌─────────────────┐  ┌───────────────────────────┐  ┌─────────────────────────┐   │
│  │                 │  │                           │  │                         │   │
│  │  DIAGNOSTIC     │  │    ANALYSIS               │  │    REMEDIATION          │   │
│  │  SERVICES       │  │    SERVICES               │  │    SERVICES             │   │
│  │                 │  │                           │  │                         │   │
│  ├─────────────────┤  ├───────────────────────────┤  ├─────────────────────────┤   │
│  │                 │  │                           │  │                         │   │
│  │ ┌─────────────┐ │  │ ┌───────────────────────┐ │  │ ┌─────────────────────┐ │   │
│  │ │ hardware-   │ │  │ │ correlation-svc       │ │  │ │ remediation-svc     │ │   │
│  │ │ diagnostics │ │  │ │                       │ │  │ │                     │ │   │
│  │ │ -svc        │ │  │ │ Operations:           │ │  │ │ Operations:         │ │   │
│  │ │             │ │  │ │ • correlate()         │ │  │ │ • generateFix()     │ │   │
│  │ │ Operations: │ │  │ │ • findRootCause()     │ │  │ │ • applyRemediation()│ │   │
│  │ │ • collect() │ │  │ │ • detectPatterns()    │ │  │ │ • validateFix()     │ │   │
│  │ │ • analyze() │ │  │ │ • clusterIssues()     │ │  │ │ • rollback()        │ │   │
│  │ │ • getHealth │ │  │ │                       │ │  │ │                     │ │   │
│  │ │ • getMetrics│ │  │ │ Consumes:             │ │  │ │ Consumes:           │ │   │
│  │ │             │ │  │ │ • hardware events     │ │  │ │ • correlation results│ │   │
│  │ │ Publishes:  │ │  │ │ • k8s events          │ │  │ │                     │ │   │
│  │ │ • infra.    │ │  │ │                       │ │  │ │ Publishes:          │ │   │
│  │ │   diagnostics│ │  │ │ Publishes:            │ │  │ │ • infra.remediation│ │   │
│  │ │   .hardware │ │  │ │ • infra.correlation   │ │  │ │   .completed        │ │   │
│  │ └─────────────┘ │  │ │   .results            │ │  │ └─────────────────────┘ │   │
│  │                 │  │ └───────────────────────┘ │  │                         │   │
│  │ ┌─────────────┐ │  │                           │  │ ┌─────────────────────┐ │   │
│  │ │ k8s-        │ │  │                           │  │ │ nlp-command-svc     │ │   │
│  │ │ diagnostics │ │  │                           │  │ │                     │ │   │
│  │ │ -svc        │ │  │                           │  │ │ Operations:         │ │   │
│  │ │             │ │  │                           │  │ │ • parseNL()         │ │   │
│  │ │ Operations: │ │  │                           │  │ │ • toKubectl()       │ │   │
│  │ │ • inspect() │ │  │                           │  │ │ • explain()         │ │   │
│  │ │ • getLogs() │ │  │                           │  │ │ • suggestCommands() │ │   │
│  │ │ • getEvents │ │  │                           │  │ │                     │ │   │
│  │ │ • getMetrics│ │  │                           │  │ └─────────────────────┘ │   │
│  │ │             │ │  │                           │  │                         │   │
│  │ │ Publishes:  │ │  │                           │  │                         │   │
│  │ │ • infra.    │ │  │                           │  │                         │   │
│  │ │   diagnostics│ │  │                           │  │                         │   │
│  │ │   .kubernetes│ │  │                           │  │                         │   │
│  │ └─────────────┘ │  │                           │  │                         │   │
│  │                 │  │                           │  │                         │   │
│  └─────────────────┘  └───────────────────────────┘  └─────────────────────────┘   │
│                                       │                                            │
│                                       ▼                                            │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    SHARED SERVICES (Cross-Domain)                             │  │
│  │                                                                               │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │  │
│  │  │ llm-gateway │  │ embedding   │  │ notification│  │ audit-svc           │  │  │
│  │  │ -svc        │  │ -svc        │  │ -svc        │  │                     │  │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────────┘  │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                       │                                            │
│                                       ▼                                            │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    DATA LAYER                                                 │  │
│  │                                                                               │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │  │
│  │  │ OpenSearch  │  │ Redis       │  │ PostgreSQL  │  │ S3/MinIO            │  │  │
│  │  │ (Vectors)   │  │ (Cache)     │  │ (Metadata)  │  │ (Logs/Reports)      │  │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────────┘  │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```

### 4.3 Service Composition Patterns

** Orchestration: Diagnostic Workflow **

For complex multi-step diagnostic processes, use centralized orchestration (Temporal/LangGraph):

```

┌─────────────────────────────────────────────────────────────────────────────────────┐
│                    DIAGNOSTIC WORKFLOW (Orchestration Pattern)                      │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                    WORKFLOW ORCHESTRATOR (Temporal)                         │   │
│  │                                                                             │   │
│  │    ┌─────┐     ┌─────┐     ┌─────┐     ┌─────┐     ┌─────┐                 │   │
│  │    │  1  │────▶│  2  │────▶│  3  │────▶│  4  │────▶│  5  │                 │   │
│  │    │     │     │     │     │     │     │     │     │     │                 │   │
│  │    │Parse│     │Diag-│     │Corre│     │Remed│     │Noti-│                 │   │
│  │    │Query│     │nose │     │late │     │iate │     │fy   │                 │   │
│  │    └─────┘     └─────┘     └─────┘     └─────┘     └─────┘                 │   │
│  │       │           │           │           │           │                     │   │
│  │       ▼           ▼           ▼           ▼           ▼                     │   │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐               │   │
│  │  │nlp-cmd- │ │hardware-│ │correlat-│ │remediat-│ │notifica-│               │   │
│  │  │svc      │ │diag-svc │ │ion-svc  │ │ion-svc  │ │tion-svc │               │   │
│  │  └─────────┘ │+ k8s-   │ └─────────┘ └─────────┘ └─────────┘               │   │
│  │              │diag-svc │                                                    │   │
│  │              └─────────┘                                                    │   │
│  │                                                                             │   │
│  │  Compensation Logic:                                                        │   │
│  │  • Step 4 fails → Rollback remediation, notify with partial results        │   │
│  │  • Step 3 fails → Return diagnostic data without correlation               │   │
│  │  • Any timeout → Return partial results, schedule retry                    │   │
│  │                                                                             │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```

### Choreography: Event-Driven Alerts

For loosely coupled reactive scenarios, use event-driven choreography:

```

┌─────────────────────────────────────────────────────────────────────────────────────┐
│                    ALERT WORKFLOW (Choreography Pattern)                            │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌──────────────┐                                                                  │
│  │ hardware-    │──publish──▶ infra.diagnostics.hardware                           │
│  │ diagnostics  │            (event: ThresholdExceeded)                            │
│  └──────────────┘                     │                                            │
│                                       │                                            │
│         ┌─────────────────────────────┼─────────────────────────────┐              │
│         │                             │                             │              │
│         ▼                             ▼                             ▼              │
│  ┌──────────────┐            ┌──────────────┐            ┌──────────────┐          │
│  │ correlation  │            │ notification │            │ audit-svc    │          │
│  │ -svc         │            │ -svc         │            │              │          │
│  │              │            │              │            │ (logs event) │          │
│  │ (searches    │            │ (sends Slack │            └──────────────┘          │
│  │  patterns)   │            │  alert)      │                                      │
│  └──────┬───────┘            └──────────────┘                                      │
│         │                                                                          │
│         │ publish                                                                  │
│         ▼                                                                          │
│  infra.correlation.results                                                         │
│  (event: PatternMatched)                                                           │
│         │                                                                          │
│         ├─────────────────────────────┬─────────────────────────────┐              │
│         ▼                             ▼                             ▼              │
│  ┌──────────────┐            ┌──────────────┐            ┌──────────────┐          │
│  │ remediation  │            │ notification │            │ retrieval    │          │
│  │ -svc         │            │ -svc         │            │ -svc         │          │
│  │              │            │              │            │ (KnowledgeAI)│          │
│  │ (generates   │            │ (sends       │            │              │          │
│  │  fix)        │            │  detailed    │            │ (finds       │          │
│  └──────────────┘            │  alert)      │            │  related KB) │          │
│                              └──────────────┘            └──────────────┘          │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```

### 4.4 Service Contracts Summary

| Service                     | Key Operations                              | QoS Targets                  | Dependencies                                      |
|-----------------------------|---------------------------------------------|------------------------------|---------------------------------------------------|
| **hardware-diagnostics-svc** | `collect()`, `analyze()`, `getHealth()`    | P99: 500ms, 99.9% avail     | SSH access, notification-svc                     |
| **k8s-diagnostics-svc**      | `inspect()`, `getLogs()`, `getEvents()`    | P99: 400ms, 99.9% avail     | K8s API, notification-svc                        |
| **correlation-svc**          | `correlate()`, `findRootCause()`           | P99: 600ms, 99.5% avail     | Both diagnostic services, retrieval-svc          |
| **remediation-svc**          | `generateFix()`, `applyRemediation()`      | P99: 1s, 99.5% avail        | llm-gateway-svc, correlation-svc                 |
| **nlp-command-svc**          | `parseNL()`, `toKubectl()`                 | P99: 200ms, 99.9% avail     | llm-gateway-svc                                  |

---

## PART 5: KNOWLEDGEAI PLATFORM (SOA Design)

### 5.1 System Overview

| Attribute         | Description                                                                 |
|-------------------|-----------------------------------------------------------------------------|
| **Name**          | Unified Knowledge & Support Intelligence Platform                          |
| **Codename**      | **KnowledgeAI**                                                             |
| **SOA Domain**    | Knowledge Management                                                        |
| **Purpose**       | Centralized AI-driven knowledge retrieval, support automation, and intelligent assistance |
| **Service Count** | 8 domain services + 5 channel adapters + 5 shared services                 |

### 5.2 Service Architecture


```

┌─────────────────────────────────────────────────────────────────────────────────────┐
│                           KNOWLEDGEAI SOA ARCHITECTURE                              │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    CHANNEL ADAPTERS (Presentation Layer)                      │  │
│  │                                                                               │  │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────────┐  │  │
│  │  │ slack-  │ │ forum-  │ │confluenc│ │ jira-   │ │salesforc│ │ web-ui-     │  │  │
│  │  │ adapter │ │ adapter │ │e-adapter│ │ adapter │ │e-adapter│ │ adapter     │  │  │
│  │  │ -svc    │ │ -svc    │ │ -svc    │ │ -svc    │ │ -svc    │ │ (React)     │  │  │
│  │  └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘ └──────┬──────┘  │  │
│  │       │           │           │           │           │             │         │  │
│  └───────┼───────────┼───────────┼───────────┼───────────┼─────────────┼─────────┘  │
│          │           │           │           │           │             │            │
│          └───────────┴───────────┴───────────┴───────────┴─────────────┘            │
│                                       │                                            │
│                                       ▼                                            │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    API GATEWAY (Kong)                                         │  │
│  │                    /api/v1/knowledge/*                                        │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                       │                                            │
│                                       ▼                                            │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    ESB (Kafka + Redis Streams)                                │  │
│  │                    knowledge.* topics                                         │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                       │                                            │
│    ┌──────────────────────────────────┼──────────────────────────────────┐         │
│    │                                  │                                  │         │
│    ▼                                  ▼                                  ▼         │
│  ┌───────────────────┐  ┌────────────────────────┐  ┌─────────────────────────┐   │
│  │                   │  │                        │  │                         │   │
│  │  QUERY SERVICES   │  │  CONTENT SERVICES      │  │  AUTOMATION SERVICES    │   │
│  │                   │  │                        │  │                         │   │
│  ├───────────────────┤  ├────────────────────────┤  ├─────────────────────────┤   │
│  │                   │  │                        │  │                         │   │
│  │┌─────────────────┐│  │┌──────────────────────┐│  │┌───────────────────────┐│   │
│  ││query-           ││  ││retrieval-svc         ││  ││ticket-automation-svc  ││   │
│  ││classification   ││  ││                      ││  ││                       ││   │
│  ││-svc             ││  ││Operations:           ││  ││Operations:            ││   │
│  ││                 ││  ││• search()            ││  ││• createTicket()       ││   │
│  ││Operations:      ││  ││• hybridSearch()      ││  ││• linkCases()          ││   │
│  ││• classify()     ││  ││• rerank()            ││  ││• syncStatus()         ││   │
│  ││• extractEntities││  ││• getRelated()        ││  ││• autoAssign()         ││   │
│  ││• detectIntent() ││  ││                      ││  ││                       ││   │
│  │└─────────────────┘│  │└──────────────────────┘│  │└───────────────────────┘│   │
│  │                   │  │                        │  │                         │   │
│  │┌─────────────────┐│  │┌──────────────────────┐│  │┌───────────────────────┐│   │
│  ││resolution-      ││  ││ingestion-svc         ││  ││reporting-svc          ││   │
│  ││synthesis-svc    ││  ││                      ││  ││                       ││   │
│  ││                 ││  ││Operations:           ││  ││Operations:            ││   │
│  ││Operations:      ││  ││• ingest()            ││  ││• generateReport()     ││   │
│  ││• synthesize()   ││  ││• extract()           ││  ││• syncData()           ││   │
│  ││• generateAnswer ││  ││• chunk()             ││  ││• getAnalytics()       ││   │
│  ││• scoreConfidence││  ││• embed()             ││  ││• exportData()         ││   │
│  │└─────────────────┘│  │└──────────────────────┘│  │└───────────────────────┘│   │
│  │                   │  │                        │  │                         │   │
│  │                   │  │┌──────────────────────┐│  │┌───────────────────────┐│   │
│  │                   │  ││summarization-svc     ││  ││proactive-monitoring   ││   │
│  │                   │  ││                      ││  ││-svc                   ││   │
│  │                   │  ││Operations:           ││  ││                       ││   │
│  │                   │  ││• summarizeThread()   ││  ││Operations:            ││   │
│  │                   │  ││• summarizeDoc()      ││  ││• watchWebhooks()      ││   │
│  │                   │  ││• extractDecisions()  ││  ││• detectSimilar()      ││   │
│  │                   │  ││• formatStyle()       ││  ││• alertOnPattern()     ││   │
│  │                   │  │└──────────────────────┘│  │└───────────────────────┘│   │
│  │                   │  │                        │  │                         │   │
│  └───────────────────┘  └────────────────────────┘  └─────────────────────────┘   │
│                                       │                                            │
│                                       ▼                                            │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    SHARED SERVICES (Cross-Domain)                             │  │
│  │                                                                               │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │  │
│  │  │ llm-gateway │  │ embedding   │  │ notification│  │ audit-svc           │  │  │
│  │  │ -svc        │  │ -svc        │  │ -svc        │  │                     │  │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────────┘  │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                       │                                            │
│                                       ▼                                            │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    DATA LAYER                                                 │  │
│  │                                                                               │  │
│  │  ┌──────────────────────────────────────────────────────────────────────────┐ │  │
│  │  │                    VECTOR DATABASE (OpenSearch)                          │ │  │
│  │  │                                                                          │ │  │
│  │  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐       │ │  │
│  │  │  │Confluence│ │  Jira    │ │  Slack   │ │ GitHub   │ │Salesforce│       │ │  │
│  │  │  │  Index   │ │  Index   │ │  Index   │ │  Index   │ │  Index   │       │ │  │
│  │  │  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘       │ │  │
│  │  │                                                                          │ │  │
│  │  │  ┌──────────┐ ┌──────────┐ ┌──────────┐                                  │ │  │
│  │  │  │Community │ │  MDX     │ │ Product  │                                  │ │  │
│  │  │  │  Index   │ │  Index   │ │  Index   │                                  │ │  │
│  │  │  └──────────┘ └──────────┘ └──────────┘                                  │ │  │
│  │  │                                                                          │ │  │
│  │  └──────────────────────────────────────────────────────────────────────────┘ │  │
│  │                                                                               │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │  │
│  │  │ Redis       │  │ PostgreSQL  │  │ S3/MinIO    │  │ Neo4j (Optional)    │  │  │
│  │  │ (Cache)     │  │ (Metadata)  │  │ (Documents) │  │ (Knowledge Graph)   │  │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────────┘  │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```

### 5.3 Service Composition Patterns

**Orchestration: Support Query Workflow**

```

┌─────────────────────────────────────────────────────────────────────────────────────┐
│                    SUPPORT QUERY WORKFLOW (Orchestration Pattern)                   │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                    WORKFLOW ORCHESTRATOR (LangGraph)                        │   │
│  │                                                                             │   │
│  │    ┌─────┐     ┌─────┐     ┌─────┐     ┌─────┐     ┌─────┐                 │   │
│  │    │  1  │────▶│  2  │────▶│  3  │────▶│  4  │────▶│  5  │                 │   │
│  │    │     │     │     │     │     │     │     │     │     │                 │   │
│  │    │Class│     │Retri│     │Synth│     │Valid│     │Resp-│                 │   │
│  │    │ify  │     │eve  │     │esize│     │ate  │     │ond  │                 │   │
│  │    └─────┘     └─────┘     └─────┘     └─────┘     └─────┘                 │   │
│  │       │           │           │           │           │                     │   │
│  │       ▼           ▼           ▼           ▼           ▼                     │   │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐               │   │
│  │  │query-   │ │retrieval│ │resolution│ │llm-gate │ │channel  │               │   │
│  │  │classific│ │-svc     │ │-synthesis│ │way-svc  │ │adapter  │               │   │
│  │  │ation-svc│ │         │ │-svc      │ │(ground) │ │         │               │   │
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘               │   │
│  │                                                                             │   │
│  │  Branching Logic:                                                           │   │
│  │  • Jira ID detected    → Direct lookup + context retrieval                  │   │
│  │  • SF case detected    → Salesforce API + similar case search               │   │
│  │  • Code question       → GitHub index + documentation search                │   │
│  │  • General question    → Full hybrid search across all sources              │   │
│  │                                                                             │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```

### Choreography: Proactive Case Monitoring

```

┌─────────────────────────────────────────────────────────────────────────────────────┐
│                    PROACTIVE MONITORING (Choreography Pattern)                      │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  EXTERNAL TRIGGER                                                                   │
│  ┌──────────────┐                                                                  │
│  │ Jira Webhook │──publish──▶ knowledge.ingestion.new-ticket                       │
│  │ (new ticket) │            (event: NewTicketCreated)                             │
│  └──────────────┘                     │                                            │
│                                       │                                            │
│         ┌─────────────────────────────┼─────────────────────────────┐              │
│         │                             │                             │              │
│         ▼                             ▼                             ▼              │
│  ┌──────────────┐            ┌──────────────┐            ┌──────────────┐          │
│  │ proactive-   │            │ ingestion    │            │ audit-svc    │          │
│  │ monitoring   │            │ -svc         │            │              │          │
│  │ -svc         │            │              │            │ (logs event) │          │
│  │              │            │ (indexes     │            └──────────────┘          │
│  │ (similarity  │            │  ticket)     │                                      │
│  │  search)     │            └──────────────┘                                      │
│  └──────┬───────┘                                                                  │
│         │                                                                          │
│         │ (if similar cases found)                                                 │
│         │ publish                                                                  │
│         ▼                                                                          │
│  knowledge.proactive.similar-found                                                 │
│  (event: SimilarCasesFound)                                                        │
│         │                                                                          │
│         ├─────────────────────────────┬─────────────────────────────┐              │
│         ▼                             ▼                             ▼              │
│  ┌──────────────┐            ┌──────────────┐            ┌──────────────┐          │
│  │ resolution-  │            │ notification │            │ ticket-      │          │
│  │ synthesis    │            │ -svc         │            │ automation   │          │
│  │ -svc         │            │              │            │ -svc         │          │
│  │              │            │ (sends Slack │            │              │          │
│  │ (generates   │            │  notification│            │ (auto-links  │          │
│  │  suggested   │            │  with        │            │  related     │          │
│  │  resolution) │            │  similar     │            │  tickets)    │          │
│  └──────────────┘            │  cases)      │            └──────────────┘          │
│                              └──────────────┘                                      │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```

### 5.4 Service Contracts Summary

| Service                        | Key Operations                                      | QoS Targets                  | Dependencies                                      |
|--------------------------------|-----------------------------------------------------|------------------------------|---------------------------------------------------|
| **retrieval-svc**              | `search()`, `hybridSearch()`, `rerank()`           | P99: 320ms, 99.9% avail     | embedding-svc, OpenSearch                        |
| **ingestion-svc**              | `ingest()`, `extract()`, `chunk()`, `embed()`      | P99: 1.2s, 99.5% avail      | embedding-svc, OpenSearch                        |
| **summarization-svc**          | `summarizeThread()`, `formatStyle()`               | P99: 680ms, 99.5% avail     | llm-gateway-svc                                  |
| **query-classification-svc**   | `classify()`, `extractEntities()`                  | P99: 150ms, 99.9% avail     | llm-gateway-svc                                  |
| **resolution-synthesis-svc**   | `synthesize()`, `scoreConfidence()`                | P99: 800ms, 99.5% avail     | retrieval-svc, llm-gateway-svc                   |
| **ticket-automation-svc**      | `createTicket()`, `linkCases()`                    | P99: 420ms, 99.5% avail     | jira-adapter, salesforce-adapter                 |
| **reporting-svc**              | `generateReport()`, `syncData()`                   | P99: 890ms, 99.0% avail     | PostgreSQL, all data sources                     |
| **proactive-monitoring-svc**   | `watchWebhooks()`, `detectSimilar()`               | P99: 500ms, 99.5% avail     | retrieval-svc, notification-svc                  |

---

## PART 6: SHARED SERVICES LAYER

### 6.1 Shared Services Architecture

Following SOA reusability principles, these services are shared across both platforms:

```

┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         SHARED SERVICES LAYER                                       │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    LLM GATEWAY SERVICE                                        │  │
│  │                    llm-gateway-svc                                            │  │
│  │                                                                               │  │
│  │  Operations:                        QoS:                                      │  │
│  │  • generate(prompt, model, params)  • P99: 1.8s                               │  │
│  │  • chat(messages, model)            • Availability: 99.9%                     │  │
│  │  • embed(text) [delegation]         • Throughput: 200 req/s                   │  │
│  │                                                                               │  │
│  │  Features:                                                                    │  │
│  │  • SambaNova routing (primary)                                                │  │
│  │  • Fallback to OpenAI/Anthropic                                               │  │
│  │  • Response caching (Redis)                                                   │  │
│  │  • Rate limiting per consumer                                                 │  │
│  │  • Request/response logging                                                   │  │
│  │  • Cost tracking                                                              │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    EMBEDDING SERVICE                                          │  │
│  │                    embedding-svc                                              │  │
│  │                                                                               │  │
│  │  Operations:                        QoS:                                      │  │
│  │  • embed(text)                      • P99: 120ms                              │  │
│  │  • embedBatch(texts[])              • Availability: 99.9%                     │  │
│  │  • getSimilar(embedding, top_k)     • Throughput: 1000 req/s                  │  │
│  │                                                                               │  │
│  │  Features:                                                                    │  │
│  │  • BGE-large-en-v1.5 (primary)                                                │  │
│  │  • E5-large fallback                                                          │  │
│  │  • Embedding cache (Redis, 24h TTL)                                           │  │
│  │  • Batch processing optimization                                              │  │
│  │  • Dimension normalization                                                    │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    NOTIFICATION SERVICE                                       │  │
│  │                    notification-svc                                           │  │
│  │                                                                               │  │
│  │  Operations:                        QoS:                                      │  │
│  │  • send(channel, message, opts)     • P99: 95ms                               │  │
│  │  • sendBatch(notifications[])       • Availability: 99.9%                     │  │
│  │  • getDeliveryStatus(id)            • Throughput: 500 req/s                   │  │
│  │                                                                               │  │
│  │  Channels:                                                                    │  │
│  │  • Slack (via Bolt SDK)                                                       │  │
│  │  • Email (SMTP/SendGrid)                                                      │  │
│  │  • Webhook (HTTP POST)                                                        │  │
│  │  • Teams (optional)                                                           │  │
│  │  • PagerDuty (critical alerts)                                                │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    AUDIT SERVICE                                              │  │
│  │                    audit-svc                                                  │  │
│  │                                                                               │  │
│  │  Operations:                        QoS:                                      │  │
│  │  • log(event)                       • P99: 45ms                               │  │
│  │  • query(filters, pagination)       • Availability: 99.9%                     │  │
│  │  • export(format, dateRange)        • Throughput: 2000 req/s                  │  │
│  │                                                                               │  │
│  │  Features:                                                                    │  │
│  │  • Immutable event log (append-only)                                          │  │
│  │  • Compliance support (SOC2, GDPR)                                            │  │
│  │  • Retention policies                                                         │  │
│  │  • Searchable audit trail                                                     │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    METRICS SERVICE                                            │  │
│  │                    metrics-svc                                                │  │
│  │                                                                               │  │
│  │  Operations:                        QoS:                                      │  │
│  │  • record(metric, value, tags)      • P99: 35ms                               │  │
│  │  • query(metric, timeRange)         • Availability: 99.9%                     │  │
│  │  • aggregate(metrics[], func)       • Throughput: 5000 req/s                  │  │
│  │                                                                               │  │
│  │  Features:                                                                    │  │
│  │  • Prometheus exposition format                                               │  │
│  │  • Custom business metrics                                                    │  │
│  │  • Aggregation functions                                                      │  │
│  │  • Grafana integration                                                        │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```

### 6.2 Cross-Platform Integration

Following SOA composability principles, the two platforms integrate through shared services and event-driven communication:

```

┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         CROSS-PLATFORM INTEGRATION                                  │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│                  InfraAI                              KnowledgeAI                   │
│             ┌─────────────────┐                  ┌─────────────────┐                │
│             │                 │                  │                 │                │
│             │ correlation-svc │                  │ retrieval-svc   │                │
│             │                 │                  │                 │                │
│             └────────┬────────┘                  └────────┬────────┘                │
│                      │                                    │                         │
│                      │                                    │                         │
│                      ▼                                    ▼                         │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                                                                               │  │
│  │                         ENTERPRISE SERVICE BUS                                │  │
│  │                                                                               │  │
│  │    ┌─────────────────────────────────────────────────────────────────────┐   │  │
│  │    │                    INTEGRATION EVENTS                               │   │  │
│  │    │                                                                     │   │  │
│  │    │  cross.infra-to-knowledge.diagnostic-query                          │   │  │
│  │    │  cross.knowledge-to-infra.resolution-found                          │   │  │
│  │    │  cross.shared.notification-request                                  │   │  │
│  │    │  cross.shared.audit-event                                           │   │  │
│  │    │                                                                     │   │  │
│  │    └─────────────────────────────────────────────────────────────────────┘   │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    INTEGRATION PATTERNS                                       │  │
│  │                                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────┐ │  │
│  │  │ Pattern 1: Diagnostic Enrichment                                       │ │  │
│  │  │                                                                         │ │  │
│  │  │ InfraAI detects error → queries KnowledgeAI for documentation         │ │  │
│  │  │ → enriches diagnostic report with relevant KB articles                 │ │  │
│  │  └─────────────────────────────────────────────────────────────────────────┘ │  │
│  │                                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────┐ │  │
│  │  │ Pattern 2: Auto-Resolution                                              │ │  │
│  │  │                                                                         │ │  │
│  │  │ KnowledgeAI finds similar past case → sends resolution to InfraAI     │ │  │
│  │  │ → InfraAI applies auto-remediation if confidence > threshold           │ │  │
│  │  └─────────────────────────────────────────────────────────────────────────┘ │  │
│  │                                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────┐ │  │
│  │  │ Pattern 3: Proactive Alerting                                           │ │  │
│  │  │                                                                         │ │  │
│  │  │ InfraAI detects pattern → KnowledgeAI searches for preventive docs     │ │  │
│  │  │ → notification-svc sends combined alert with diagnosis + documentation │ │  │
│  │  └─────────────────────────────────────────────────────────────────────────┘ │  │
│  │                                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────────────────────┐ │  │
│  │  │ Pattern 4: Knowledge Generation                                         │ │  │
│  │  │                                                                         │ │  │
│  │  │ InfraAI resolves issue → generates runbook → sends to KnowledgeAI      │ │  │
│  │  │ → ingestion-svc indexes new knowledge for future queries               │ │  │
│  │  └─────────────────────────────────────────────────────────────────────────┘ │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```

### PART 7: SECURITY ARCHITECTURE

#### 7.1 SOA Security Model

Following SOA security best practices, implement defense in depth:

```

┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         SOA SECURITY ARCHITECTURE                                   │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    PERIMETER SECURITY                                         │  │
│  │                                                                               │  │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────────────┐   │  │
│  │  │   WAF           │  │   DDoS          │  │   TLS Termination           │   │  │
│  │  │   (Cloudflare)  │  │   Protection    │  │   (Kong Gateway)            │   │  │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────────────────┘   │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    AUTHENTICATION & AUTHORIZATION                             │  │
│  │                                                                               │  │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────────────┐   │  │
│  │  │   Identity      │  │   Token         │  │   Policy Engine             │   │  │
│  │  │   Provider      │  │   Service       │  │   (Open Policy Agent)       │   │  │
│  │  │   (Okta/Azure)  │  │   (JWT/OAuth2)  │  │                             │   │  │
│  │  │                 │  │                 │  │   Policies:                 │   │  │
│  │  │   • SSO         │  │   • Token issue │  │   • RBAC rules              │   │  │
│  │  │   • MFA         │  │   • Refresh     │  │   • Scope enforcement       │   │  │
│  │  │   • Federation  │  │   • Revocation  │  │   • Resource ACLs           │   │  │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────────────────┘   │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    SERVICE-TO-SERVICE SECURITY                                │  │
│  │                                                                               │  │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────────────┐   │  │
│  │  │   mTLS          │  │   Service       │  │   Secret Management         │   │  │
│  │  │   (Envoy/Istio) │  │   Accounts      │  │   (Vault)                   │   │  │
│  │  │                 │  │   (Kubernetes)  │  │                             │   │  │
│  │  │   • Cert rotation│ │   • Identity    │  │   • API keys                │   │  │
│  │  │   • Encryption  │  │   • SPIFFE      │  │   • DB credentials          │   │  │
│  │  │   • Verification│  │   • Attestation │  │   • LLM tokens              │   │  │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────────────────┘   │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    DATA SECURITY                                              │  │
│  │                                                                               │  │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────────────────┐   │  │
│  │  │   Encryption    │  │   Data          │  │   Audit Logging             │   │  │
│  │  │   at Rest       │  │   Classification│  │   (audit-svc)               │   │  │
│  │  │   (AES-256)     │  │                 │  │                             │   │  │
│  │  │                 │  │   • PII tagging │  │   • All data access         │   │  │
│  │  │   • DB encrypt  │  │   • Sensitivity │  │   • State changes           │   │  │
│  │  │   • Object store│  │   • Retention   │  │   • Auth events             │   │  │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────────────────┘   │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘

```


### 7.2 Service Authorization Matrix

| Consumer      | retrieval-svc          | llm-gateway-svc       | hardware-diagnostics-svc       | ticket-automation-svc     |
|---------------|------------------------|-----------------------|--------------------------------|---------------------------|
| **Web UI**    | `knowledge:read`      | `llm:generate`       | `infra:read`                  | `tickets:create`         |
| **Slack Bot** | `knowledge:read`      | `llm:generate`       | `infra:read`                  | `tickets:create`         |
| **CLI Tool**  | `knowledge:read`      | `llm:generate`       | `infra:read,write`            | —                        |
| **CI/CD**     | —                     | —                    | `infra:read`                  | `tickets:read`           |
| **Admin**     | `knowledge:*`         | `llm:*`              | `infra:*`                     | `tickets:*`              |

---

## PART 8: TECHNOLOGY CONSOLIDATION

### 8.1 Before vs After (SOA Transformation)

| Component              | Before (12 Initiatives)                          | After (SOA)                          | Consolidation     |
|------------------------|--------------------------------------------------|--------------------------------------|-------------------|
| **Vector Databases**   | 4 (FAISS x2, OpenSearch, Custom)                | 1 (OpenSearch)                      | **75% reduction** |
| **LLM Integrations**   | 5 (multiple direct integrations)                | 1 (llm-gateway-svc)                 | **80% reduction** |
| **Embedding Pipelines**| 4                                                | 1 (embedding-svc)                   | **75% reduction** |
| **Slack Bots**         | 3 separate bots                                  | 1 (slack-adapter-svc)               | **67% reduction** |
| **Web UIs**            | 3 (React x2, Custom)                             | 1 (unified React)                   | **67% reduction** |
| **API Frameworks**     | 2 (FastAPI, Flask)                               | 1 (FastAPI)                         | **50% reduction** |
| **Service Instances**  | N/A (monoliths)                                  | 18 microservices                    | Proper decomposition |
| **Governance**         | None                                             | SOA Governance Framework            | New capability    |

### 8.2 Unified Technology Stack

| Layer                | Technology                  | Purpose                                   | SOA Role                     |
|----------------------|-----------------------------|-------------------------------------------|------------------------------|
| **API Gateway**      | Kong                        | Unified entry point, cross-cutting concerns | Service Consumer Interface  |
| **Service Registry** | Consul                      | Service discovery, health monitoring      | Service Registry            |
| **ESB**              | Kafka + Redis Streams       | Event routing, async communication        | Message Broker              |
| **Orchestration**    | Temporal / LangGraph        | Workflow coordination                     | Service Composition         |
| **API Framework**    | FastAPI                     | Service implementation                    | Service Provider            |
| **LLM**              | SambaNova (via gateway)     | AI capabilities                           | Shared Service              |
| **Embeddings**       | BGE-large, E5-large         | Text embeddings                           | Shared Service              |
| **Vector DB**        | OpenSearch                  | Hybrid search                             | Data Service                |
| **Cache**            | Redis                       | Session & result caching                  | Infrastructure              |
| **Relational DB**    | PostgreSQL                  | Metadata, audit                           | Data Service                |
| **Object Storage**   | S3/MinIO                    | Documents, logs                           | Data Service                |
| **Monitoring**       | Prometheus/Grafana          | Observability                             | Infrastructure              |
| **Tracing**          | OpenTelemetry/Jaeger        | Distributed tracing                       | Infrastructure              |

---

## PART 9: IMPLEMENTATION ROADMAP

### 9.1 Phased Implementation

Following **SOA adoption best practices**, implement incrementally:

#### Phase 1: Foundation (Weeks 1-4)

| Task                              | Description                                      | Owner           | Deliverable                          |
|-----------------------------------|--------------------------------------------------|-----------------|--------------------------------------|
| Deploy Service Registry           | Set up Consul cluster                            | Platform Team   | Running Consul cluster              |
| Implement API Gateway             | Configure Kong with auth                         | Platform Team   | Gateway accepting requests          |
| Deploy ESB                        | Kafka cluster, topic design                      | Platform Team   | Event infrastructure                |
| Create Governance Portal          | Service catalog, contract repository             | Platform Team   | Governance tooling                  |
| Define Service Contracts          | OpenAPI/Proto for all services                   | All Teams       | Contract specifications             |

#### Phase 2: Shared Services (Weeks 5-8)

| Task                        | Description                               | Owner           | Deliverable                       |
|-----------------------------|-------------------------------------------|-----------------|-----------------------------------|
| Deploy llm-gateway-svc      | LLM routing, caching, fallback            | Platform Team   | Production LLM service           |
| Deploy embedding-svc        | Embedding generation, caching             | Platform Team   | Production embedding service     |
| Deploy notification-svc     | Multi-channel notifications               | Platform Team   | Production notification service  |
| Deploy audit-svc            | Compliance logging                        | Platform Team   | Production audit service         |
| Deploy metrics-svc          | Prometheus metrics                        | Platform Team   | Production metrics service       |

#### Phase 3: InfraAI Services (Weeks 9-14)

| Task                            | Description                              | Owner                    | Deliverable                          |
|---------------------------------|------------------------------------------|--------------------------|--------------------------------------|
| Deploy hardware-diagnostics-svc | Migrate XRDU tool                        | Prafull Thokal          | Production diagnostic service       |
| Deploy k8s-diagnostics-svc      | Migrate Sambastack Inspector             | Prafull/Raj/Divyesh     | Production K8s service              |
| Deploy correlation-svc          | Pattern matching, root cause             | Prafull                 | Production correlation service      |
| Deploy remediation-svc          | Code generation, auto-fix                | Rohit Vyawahare         | Production remediation service      |
| Deploy nlp-command-svc          | NL to kubectl                            | Prajwal Balapure        | Production NLP service              |
| Implement InfraAI workflows     | Temporal orchestration                   | Platform Team           | Working orchestration               |

#### Phase 4: KnowledgeAI Services (Weeks 15-22)

| Task                               | Description                            | Owner                  | Deliverable                          |
|------------------------------------|----------------------------------------|------------------------|--------------------------------------|
| Deploy retrieval-svc               | Hybrid search engine                   | Hitesh Jha            | Production retrieval service        |
| Deploy ingestion-svc               | Document processing pipeline           | Hitesh Jha            | Production ingestion service        |
| Deploy summarization-svc           | Thread/doc summarization               | Tanish/Neha           | Production summarization service    |
| Deploy query-classification-svc    | Intent detection                       | Hitesh Jha            | Production classification service   |
| Deploy resolution-synthesis-svc    | Answer generation                      | Hitesh Jha            | Production synthesis service        |
| Deploy ticket-automation-svc       | Jira/SF automation                     | Neha Marathe          | Production ticket service           |
| Deploy reporting-svc               | Analytics, sync                        | Tanish/Saurabh        | Production reporting service        |
| Deploy proactive-monitoring-svc    | Webhook monitoring                     | Hitesh Jha            | Production monitoring service       |
| Deploy channel adapters            | Slack, Confluence, Jira, SF            | Various               | Production adapters                 |

#### Phase 5: Integration & Optimization (Weeks 23-26)

| Task                          | Description                        | Owner         | Deliverable                          |
|-------------------------------|------------------------------------|---------------|--------------------------------------|
| Cross-platform integration    | InfraAI ↔ KnowledgeAI flows        | All Teams     | Working cross-platform workflows    |
| Performance tuning            | SLA optimization                   | Platform Team | Meeting SLA targets                 |
| Governance enforcement        | Automated compliance               | Platform Team | Governance automation               |
| Documentation                 | Service docs, runbooks             | All Teams     | Complete documentation              |
| Migration completion          | Legacy tool sunset                 | All Teams     | Full migration                      |

---

## PART 10: SUCCESS METRICS

### 10.1 SOA Health Metrics

| Metric                  | Target             | Measurement                     |
|-------------------------|--------------------|---------------------------------|
| **Service Availability**| 99.9% per service | Consul health checks           |
| **Contract Compliance** | 100%              | Governance portal              |
| **Service Reuse Rate**  | 60%               | Dependency graph analysis      |
| **Mean Time to Deploy** | <30 minutes       | CI/CD metrics                  |
| **Service Discovery Latency** | <10ms        | Consul metrics                 |

### 10.2 System Performance Metrics

| Metric                     | Target                                      | Measurement          |
|----------------------------|---------------------------------------------|----------------------|
| **API Response Time (P99)**| <500ms (simple), <2s (complex)             | Prometheus          |
| **Event Processing Latency**| <100ms                                     | Kafka metrics       |
| **System Uptime**          | 99.9%                                      | Monitoring stack    |
| **Concurrent Users**       | 500+                                       | Load testing        |

### 10.3 Business Impact Metrics

| Metric                     | Target          | Source                     |
|----------------------------|-----------------|----------------------------|
| **MTTR Reduction**         | 50-70%         | Support ticket analysis   |
| **Search Time Reduction**  | 70%            | User surveys              |
| **First-Response Accuracy**| 90%+           | Feedback scoring          |
| **Infrastructure Savings** | 60-75%         | Resource utilization      |
| **Development Velocity**   | +40%           | Sprint metrics            |

---

## APPENDIX A: SERVICE CONTRACT EXAMPLES

### A.1 hardware-diagnostics-svc Contract

```yaml
service:
  name: "hardware-diagnostics-svc"
  version: "1.2.0"
  domain: "infra"
  owner: "Prafull Thokal"

interface:
  protocol: "REST/gRPC"
  base_path: "/api/v1/infra/hardware-diagnostics"

  operations:
    - name: "collect"
      method: "POST"
      path: "/collect"
      description: "Collect sensor data from XRDU nodes"
      input:
        schema:
          type: "object"
          required: ["node_ids"]
          properties:
            node_ids:
              type: "array"
              items: { type: "string" }
            sensor_types:
              type: "array"
              items:
                type: "string"
                enum: ["voltage", "current", "temperature", "fan", "pcie"]
      output:
        schema:
          type: "object"
          properties:
            results:
              type: "array"
              items:
                type: "object"
                properties:
                  node_id: { type: "string" }
                  sensors: { type: "object" }
                  timestamp: { type: "string", format: "date-time" }
            collection_id: { type: "string" }

    - name: "analyze"
      method: "POST"
      path: "/analyze"
      description: "Analyze collected sensor data against thresholds"
      input:
        schema:
          type: "object"
          required: ["collection_id"]
          properties:
            collection_id: { type: "string" }
            threshold_profile: { type: "string", default: "default" }
      output:
        schema:
          type: "object"
          properties:
            status: { type: "string", enum: ["NORMAL", "WARNING", "CRITICAL", "UNKNOWN"] }
            violations:
              type: "array"
              items:
                type: "object"
                properties:
                  sensor: { type: "string" }
                  value: { type: "number" }
                  threshold: { type: "number" }
                  severity: { type: "string" }

quality_of_service:
  availability: "99.9%"
  latency:
    p50: "200ms"
    p95: "400ms"
    p99: "500ms"
  throughput: "100 req/s"

dependencies:
  required:
    - service: "notification-svc"
      version: "^1.0"
  optional:
    - service: "correlation-svc"
      version: "^2.0"

events:
  produces:
    - topic: "infra.diagnostics.hardware"
      events:
        - "SensorDataCollected"
        - "ThresholdExceeded"
        - "NodeHealthChanged"

```  yaml


### A.2 retrieval-svc Contract

```yaml

service:
  name: "retrieval-svc"
  version: "2.0.0"
  domain: "knowledge"
  owner: "Hitesh Jha"

interface:
  protocol: "REST/gRPC"
  base_path: "/api/v1/knowledge/retrieval"

  operations:
    - name: "search"
      method: "POST"
      path: "/search"
      description: "Execute hybrid search across knowledge sources"
      input:
        schema:
          type: "object"
          required: ["query"]
          properties:
            query: { type: "string", maxLength: 2000 }
            sources:
              type: "array"
              items:
                type: "string"
                enum: ["confluence", "jira", "slack", "github", "salesforce", "community"]
            filters:
              type: "object"
              properties:
                date_from: { type: "string", format: "date" }
                date_to: { type: "string", format: "date" }
                author: { type: "string" }
            top_k: { type: "integer", default: 10, minimum: 1, maximum: 100 }
            include_embeddings: { type: "boolean", default: false }
      output:
        schema:
          type: "object"
          properties:
            results:
              type: "array"
              items:
                type: "object"
                properties:
                  id: { type: "string" }
                  content: { type: "string" }
                  source: { type: "string" }
                  score: { type: "number" }
                  metadata: { type: "object" }
            total_results: { type: "integer" }
            query_time_ms: { type: "integer" }

    - name: "rerank"
      method: "POST"
      path: "/rerank"
      description: "Rerank results using cross-encoder"
      input:
        schema:
          type: "object"
          required: ["query", "results"]
          properties:
            query: { type: "string" }
            results:
              type: "array"
              items:
                type: "object"
                properties:
                  id: { type: "string" }
                  content: { type: "string" }
            top_k: { type: "integer", default: 5 }
      output:
        schema:
          type: "object"
          properties:
            reranked_results:
              type: "array"
              items:
                type: "object"
                properties:
                  id: { type: "string" }
                  content: { type: "string" }
                  rerank_score: { type: "number" }

quality_of_service:
  availability: "99.9%"
  latency:
    p50: "150ms"
    p95: "280ms"
    p99: "320ms"
  throughput: "500 req/s"

dependencies:
  required:
    - service: "embedding-svc"
      version: "^1.0"
    - service: "opensearch-cluster"
      type: "infrastructure"
  optional:
    - service: "llm-gateway-svc"
      version: "^2.0"
      purpose: "Query expansion"

events:
  produces:
    - topic: "knowledge.retrieval.results"
      events:
        - "SearchCompleted"
        - "NoResultsFound"
  consumes:
    - topic: "knowledge.ingestion.completed"
      events:
        - "IndexRefreshNeeded"


``` 
yaml

## APPENDIX B: GOVERNANCE CHECKLIST

### B.1 Service Onboarding Checklist

| #  | Requirement                                              | Verification              | Status |
|----|----------------------------------------------------------|---------------------------|--------|
| 1  | Service name follows `{domain}-{capability}-svc` pattern | Automated                 | ☐     |
| 2  | OpenAPI 3.0 or Proto specification provided              | Governance portal         | ☐     |
| 3  | Version follows semantic versioning                      | Automated                 | ☐     |
| 4  | Health endpoint implemented (`/health`)                  | Consul check              | ☐     |
| 5  | Metrics endpoint implemented (`/metrics`)                | Prometheus scrape         | ☐     |
| 6  | Authentication/authorization specified                   | Security review           | ☐     |
| 7  | QoS targets defined (latency, availability)              | Contract review           | ☐     |
| 8  | Dependencies declared (max 5 direct)                     | Dependency scan           | ☐     |
| 9  | No circular dependencies                                 | Automated check           | ☐     |
| 10 | README and runbook provided                              | Documentation review      | ☐     |
| 11 | Unit test coverage >80%                                  | CI pipeline               | ☐     |
| 12 | Integration tests provided                               | CI pipeline               | ☐     |
| 13 | Registered in service registry                           | Consul registration       | ☐     |
| 14 | Contract published to repository                         | Governance portal         | ☐     |
| 15 | Owner and team assigned                                  | Governance portal         | ☐     |

---

## CONCLUSION

This **SOA-optimized architecture** transforms **12 independent initiatives** into **2 well-governed, loosely coupled platforms** built on solid service-oriented principles. The key improvements include:

### Architecture Improvements:
- Clear service boundaries with standardized contracts
- Loose coupling via ESB and event-driven communication
- Shared services for common capabilities (LLM, embedding, notification)
- Service registry enabling dynamic discovery and composition
- Formal governance framework ensuring consistency

### Operational Improvements:
- **60-75% reduction** in redundant infrastructure
- Faster development through service reuse
- Independent deployment and scaling per service
- Comprehensive observability and monitoring

### Business Benefits:
- Consistent user experience across all channels
- Cross-system intelligence between **InfraAI** and **KnowledgeAI**
- Reduced maintenance overhead
- Lower operational costs
- Faster time to market for new capabilities

### Next Steps:
- Review and approve SOA architecture
- Establish governance committee
- Begin **Phase 1** implementation
- Assign service owners per contract specifications
- Create CI/CD pipelines for service deployment

**Document Maintainers:**
- **Architecture:** Platform Team
- **InfraAI Services:** Prafull Thokal
- **KnowledgeAI Services:** Hitesh Jha
- **Governance:** Platform Team

**Review Cycle:** Quarterly architecture review, continuous contract updates