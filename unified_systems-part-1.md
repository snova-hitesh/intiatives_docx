# UNIFIED AI SYSTEMS ARCHITECTURE
## Consolidating 12 Team Initiatives into 2 Powerful Platforms

---

**Document Version:** 1.0  
**Date:** 10 March 2026  
**Classification:** Internal Architecture Document  

---


# EXECUTIVE SUMMARY

This document presents a unified architecture that consolidates **12 separate team initiatives** into **2 integrated AI platforms**, eliminating redundant development efforts and creating cohesive, scalable systems.


## The Challenge

Our team has been developing multiple AI-powered tools independently:
- **5 Knowledge Retrieval & Support Bots** (Support Intelligence, Slack-Bot, Community Bot, Cleerfeed Bot, ConfluenceGPT, sf-jira-ai-agent , SF-Jira reporting App)
- **4 Infrastructure Diagnostic Tools** (XRDU Diagnostic, Sambastack Inspector, KubeAssist, kubesage)
- **1 Code Generation Tool** (AI Client Requirement Extractor)

**Problems with this approach:**
- 1.  Duplicated infrastructure (vector DBs, LLM integrations, embedding pipelines)
- 2. Inconsistent user experiences across tools
- 3. Fragmented maintenance and support
- 4. No cross-tool intelligence or data sharing
- 5. Wasted engineering effort on common components




## The Solution: Two Unified Systems

| Unified System | Codename | Consolidates | Purpose |
|---------------|----------|--------------|---------|
| **Infrastructure & Diagnostic Intelligence Platform** | InfraAI | XRDU Diagnostic, Sambastack Inspector, KubeAssist, kubesage | Unified infrastructure monitoring, diagnostics, and AI-assisted troubleshooting |
| **Knowledge & Support Intelligence Platform** | KnowledgeAI | Support Intelligence, Slack-Bot, Community Bot, Cleerfeed Bot, ConfluenceGPT | Centralized knowledge retrieval, support automation, and intelligent assistance , AI Client Req and extractor , sf-jira-ai-agent , SF-Jira reporting App |

## Key Benefits

1. **Shared Infrastructure** - Single vector DB, LLM gateway, embedding pipeline  
2. **Cross-System Intelligence** - Diagnostics can query knowledge base; support can access infrastructure status  
3. **Unified Experience** - Consistent interfaces across all channels  
4. **Reduced Maintenance** - One platform to maintain per domain  
5. **Faster Development** - Reuse common components  
6. **Cost Efficiency** - Shared resources, reduced infrastructure costs  

---

# PART 1: INITIATIVE ANALYSIS

## 1.1 Initiative Inventory

# 1.1 Initiative Inventory

| #  | Initiative                                      | Owner(s)                              | Category                          | Current Status      | Unified Target                                      |
|----|-------------------------------------------------|---------------------------------------|-----------------------------------|---------------------|-----------------------------------------------------|
| 1  | AI Client Requirement Extractor & Code Generator | Rohit Vyawahare                      | Code Generation                  | Idea                | InfraAI (remediation) + KnowledgeAI (ingestion)    |
| 2  | XRDU Diagnostic Tool                            | Prafull Thokal                       | Hardware Diagnostics             | Production-ready    | InfraAI Layer 0                                     |
| 3  | Sambastack Inspector                            | Prafull Thokal, Raj More, Divyesh    | K8s & Runtime Diagnostics        | In Progress         | InfraAI Layer 1–2                                   |
| 4  | Support Intelligence Platform                   | Hitesh Jha                           | Enterprise RAG + Agents          | Architecture        | KnowledgeAI Core                                    |
| 5  | Slack-Bot                                       | Tanish/Neha (orig. Shivani/Omkar)    | Thread Summarization             | Completed           | KnowledgeAI Slack Interface                         |
| 6  | Community Bot                                   | Tanish/Shivani                       | Discourse RAG                    | In Progress         | KnowledgeAI Forum Interface                         |
| 7  | Cleerfeed Bot                                   | Shivani Moze                         | Internal Doc Q&A                 | Completed           | KnowledgeAI Slack Interface                         |
| 8  | KubeAssist                                      | Rohit Vyawahare                      | kubectl AI Assistant             | Idea                | InfraAI CLI + Agent Layer                           |
| 9  | kubesage / k8s-buddy                            | Prajwal Balapure                     | Natural-Language K8s Assistant   | Idea                | InfraAI NLP Layer                                   |
| 10 | ConfluenceGPT                                   | Saurabh Patil / Tanish Sakate        | Confluence RAG                   | Idea                | KnowledgeAI Retrieval                               |
| 11 | sf-jira-ai-agent                                | Neha Marathe                         | Support Ticket Automation        | Planned             | KnowledgeAI (Ticket Creation Agent)                 |
| 12 | SF-Jira reporting App                           | Tanish/Saurabh                       | SF-Jira Synchronization & Reporting | Idea             | KnowledgeAI (Reporting & Sync Layer)                |

## 1.2 Common Technology Patterns (High-Frequency)

| Technology                     | Used By     | Unified Role in New Architecture                  |
|--------------------------------|-------------|---------------------------------------------------|
| **Python 3.10+**               | 11 projects | Single backend language                           |
| **SambaNova LLM API**          | 8 projects  | Centralized LLM Gateway + routing                 |
| **RAG + Embeddings**           | 6 projects  | Shared hybrid pipeline (BGE-large/E5-large)       |
| **Slack Bolt SDK**             | 4 projects  | Single unified Slack bot framework                |
| **FastAPI / LangGraph**        | 5 projects  | API + agent orchestration                         |
| **Kubernetes Python Client / Typer** | 3 projects | Unified K8s + CLI layer                      |
| **OpenSearch (proposed)**      | Multiple    | Single hybrid vector + BM25 store                 |


## 1.3 Consolidation Strategy

### Why Two Systems?

After analyzing all initiatives, two distinct domains emerge:

**Domain 1: Infrastructure & Operations**
- Focus: Monitoring, diagnostics, troubleshooting
- Data: Metrics, logs, sensor data, events
- Users: DevOps, SRE, Support Engineers
- Real-time requirements: High (seconds)

**Domain 2: Knowledge & Support**
- Focus: Information retrieval, Q&A, documentation
- Data: Documents, tickets, conversations, code
- Users: All employees, customers, community
- Real-time requirements: Medium (seconds to minutes)

---

# PART 2: UNIFIED SYSTEM 1 - INFRASTRUCTURE & DIAGNOSTIC INTELLIGENCE PLATFORM (IDIP)

## 2.1 System Overview

| Attribute | Description |
|-----------|-------------|
| **Name**     | Infrastructure & Diagnostic Intelligence Platform |
| **Codename** | InfraAI     |
| **Purpose**  | Unified infrastructure health monitoring, diagnostics, and AI-assisted troubleshooting |
| **Scope**    | Hardware (XRDU), Kubernetes clusters, runtime components, and cloud-native environments |
| **Users**    | DevOps engineers, SREs, support engineers, infrastructure teams |

## 2.2 Consolidated Initiatives

| Original Initiative | Owner | Unified Role | Status Integration |
|---------------------|-------|--------------|-------------------|
| XRDU Diagnostic Tool | Prafull Thokal | Hardware Diagnostics Engine (Layer 0) | Production-ready, integrated |
| Sambastack Inspector | Prafull Thokal | Kubernetes Control Plane Diagnostics (Layer 1) | In Progress, unified |
| KubeAssist | Rohit Vyawahare | AI-Powered kubectl Assistant (CLI Interface) | Idea, integrated |
| kubesage/k8s-buddy | Prajwal Balapure | Natural Language K8s Interface (NLP Layer) | Idea, integrated |


## 2.3 Unified Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                           PRESENTATION LAYER (Multi-Interface)                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────────┐ │
│  │   Web UI     │  │  CLI Tool    │  │  Slack Bot   │  │   Meeting Integration    │ │
│  │   (React)    │  │  (Typer)     │  │   (Bolt)     │  │   (Teams/Zoom API)       │ │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └───────────┬──────────────┘ │
└─────────┼─────────────────┼─────────────────┼──────────────────────┼────────────────┘
          │                 │                 │                      │
          └─────────────────┴─────────────────┴──────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                           API GATEWAY & ORCHESTRATION                               │
│                    (FastAPI + LangGraph Agent Orchestration)                        │
│  • Request Routing  • Authentication  • Rate Limiting  • Load Balancing            │
└─────────────────────────────────────────────────────────────────────────────────────┘
                                    │
          ┌─────────────────────────┼─────────────────────────┐
          │                         │                         │
          ▼                         ▼                         ▼
┌─────────────────────┐  ┌─────────────────────┐  ┌─────────────────────────────────┐
│   AGENT LAYER       │  │   AGENT LAYER       │  │      AGENT LAYER                │
│ (Diagnostic Agents) │  │ (Analysis Agents)   │  │  (Remediation Agents)           │
├─────────────────────┤  ├─────────────────────┤  ├─────────────────────────────────┤
│ • XRDU Agent        │  │ • Correlation Agent │  │ • Code Generation Agent         │
│ • K8s Cluster Agent │  │ • Root Cause Agent  │  │ • Auto-Remediation Agent        │
│ • Node Health Agent │  │ • Anomaly Agent     │  │ • Ticket Creation Agent         │
│ • Runtime Agent     │  │ • Prediction Agent  │  │ • Notification Agent            │
└──────────┬──────────┘  └──────────┬──────────┘  └─────────────────────────────────┘
           │                        │
           └────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         DIAGNOSTIC ENGINE LAYER                                     │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌──────────────────────────────┐  ┌─────────────────────────────────────────────┐  │
│  │   HARDWARE DIAGNOSTICS       │  │      KUBERNETES DIAGNOSTICS                 │  │
│  │   (XRDU Engine)              │  │      (K8s Engine)                           │  │
│  │  ┌────────────────────────┐  │  │  ┌──────────────────────────────────────┐  │  │
│  │  │ SSH Connector          │  │  │  │ K8s API Client                       │  │  │
│  │  │ (Paramiko)             │  │  │  │ (Official Python Client)             │  │  │
│  │  ├────────────────────────┤  │  │  ├──────────────────────────────────────┤  │  │
│  │  │ Sensor Collector       │  │  │  │ Resource Inspector                   │  │  │
│  │  │ (100+ sensors)         │  │  │  │ (Pods/Deployments/Services)          │  │  │
│  │  ├────────────────────────┤  │  │  ├──────────────────────────────────────┤  │  │
│  │  │ Threshold Engine       │  │  │  │ Log Aggregator                       │  │  │
│  │  │ (Pandas/NumPy)         │  │  │  │ (kubectl logs/stern)                 │  │  │
│  │  ├────────────────────────┤  │  │  ├──────────────────────────────────────┤  │  │
│  │  │ Log Collector          │  │  │  │ Event Analyzer                       │  │  │
│  │  │ (sshpass/SCP)          │  │  │  │ (kubectl get events)                 │  │  │
│  │  ├────────────────────────┤  │  │  ├──────────────────────────────────────┤  │  │
│  │  │ Health Aggregator      │  │  │  │ Metrics Collector                    │  │  │
│  │  │ (Component-level)      │  │  │  │ (Prometheus/metrics-server)          │  │  │
│  │  └────────────────────────┘  │  │  └──────────────────────────────────────┘  │  │
│  └──────────────────────────────┘  └─────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         AI/ML PROCESSING LAYER                                      │
├─────────────────────────────────────────────────────────────────────────────────────┤
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                         LLM ENGINE                                            │  │
│  │  ┌───────────────┐ ┌───────────────┐ ┌───────────────┐ ┌───────────────────┐  │  │
│  │  │  Reasoning    │ │ Summarization │ │  Code Gen     │ │  Explanation      │  │  │
│  │  │  Model        │ │ Model         │ │  Model        │ │  Model            │  │  │
│  │  │ (SambaNova)   │ │ (SambaNova)   │ │ (Copilot/     │ │ (SambaNova)       │  │  │
│  │  │               │ │               │ │  Gemini)      │ │                   │  │  │
│  │  └───────────────┘ └───────────────┘ └───────────────┘ └───────────────────┘  │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    NLP & INTENT PROCESSING                                    │  │
│  │  • Natural Language Query Parser (kubesage integration)                       │  │
│  │  • Intent Classification                                                      │  │
│  │  • Entity Extraction (resource names, error types)                            │  │
│  │  • Command Mapping (NL → kubectl commands)                                    │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    AUDIO PROCESSING (Meeting Integration)                     │  │
│  │  • Audio Capture (Zoom/Teams API)                                             │  │
│  │  • Speech-to-Text (Whisper)                                                   │  │
│  │  • Requirement Extraction                                                     │  │
│  │  • Code Generation                                                            │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         DATA & STORAGE LAYER                                        │
├─────────────────────────────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌─────────────────────────┐  │
│  │   Vector DB  │  │    Cache     │  │   Object     │  │    Relational           │  │
│  │  (OpenSearch │  │   (Redis)    │  │  Storage     │  │     DB                  │  │
│  │   /Qdrant)   │  │              │  │  (S3/MinIO)  │  │  (PostgreSQL)           │  │
│  │              │  │              │  │              │  │                         │  │
│  │ • Embeddings │  │ • Sessions   │  │ • Logs       │  │ • Metadata              │  │
│  │ • Knowledge  │  │ • Results    │  │ • Reports    │  │ • Audit                 │  │
│  │ • Patterns   │  │ • LLM Cache  │  │ • Code       │  │ • Configurations        │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  └─────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

## 2.4 Unified Tech Stack

| Component | Technology | Justification |
|-----------|------------|---------------|
| **API Framework** | FastAPI | Async support, auto-docs, used by Support Intel |
| **Agent Orchestration** | LangGraph | Multi-agent workflows, state management |
| **LLM Gateway** | SambaNova API | Internal LLM, consistent across all tools |
| **CLI Framework** | Typer | Modern Python CLI, used by kubesage |
| **Web UI** | React | Existing expertise, Support Intel uses it |
| **Vector DB** | OpenSearch | Hybrid search, Hitesh's recommendation |
| **Cache** | Redis | Session management, result caching |
| **K8s Client** | Official Python Client | Robust, maintained by Kubernetes team |
| **SSH Client** | Paramiko | Already used in XRDU tool |
| **Data Processing** | Pandas, NumPy | XRDU tool already uses these |

## 2.5 Key Features & Capabilities

### Multi-Layer Diagnostic Coverage

| Layer | Domain | Capabilities | Source Initiative |
|-------|--------|--------------|-------------------|
| **Layer 0** | Hardware (XRDU) | 100+ sensor monitoring, voltage/current rails, thermal sensors, PCIe/PEX, fan telemetry, PSU metrics | XRDU Diagnostic Tool |
| **Layer 1** | Kubernetes Control Plane | Cluster health, node readiness, pod/deployment status, control plane services, ingress validation | Sambastack Inspector |
| **Layer 2** | Runtime & Applications | Inference pod monitoring, OOM/crash detection, API router errors, DB replica consistency, gateway health | Sambastack Inspector |
| **Layer 3** | AI-Powered Analysis | Natural language queries, error explanations, root cause correlation, predictive detection, code generation | KubeAssist + kubesage + AI Req. Extractor |

### Natural Language Interface

**Query Types Supported:**

| Type | Examples |
|------|----------|
| **Diagnostic** | "Check XRDU health", "Why is pod crashing?", "List failed deployments" |
| **Explanation** | "Explain this error", "What is CrashLoopBackOff?", "Explain StatefulSet" |
| **Action** | "Generate fix", "Create ticket", "Notify team" |

### Intelligent Threshold Classification

| Level | Description | Action |
|-------|-------------|--------|
| **NORMAL** | Values within acceptable ranges | Continue monitoring |
| **WARNING** | Values approaching thresholds | Proactive alert |
| **CRITICAL** | Values exceeding safe limits | Immediate attention |
| **UNKNOWN** | Thresholds undefined | Manual review required |

### Multi-Interface Access

| Interface | Use Case | Tech Stack |
|-----------|----------|------------|
| Web Dashboard | Visual monitoring, reports | React, D3.js, WebSocket |
| CLI (Typer) | Power users, automation | Python Typer, Rich |
| Slack Bot | Team notifications, Q&A | Slack Bolt, Socket Mode |
| Meeting Integration | Requirements → Code | Zoom/Teams API, Whisper |
| API (REST/gRPC) | CI/CD integration | FastAPI, gRPC |

---

# PART 3: UNIFIED SYSTEM 2 - KNOWLEDGE & SUPPORT INTELLIGENCE PLATFORM (UKSIP)

## 3.1 System Overview

| Attribute | Description |
|-----------|-------------|
| **Name** | Unified Knowledge & Support Intelligence Platform |
| **Codename** | KnowledgeAI |
| **Purpose** | Centralized AI-driven knowledge retrieval, support automation, and intelligent assistance |
| **Scope** | All internal and external communication channels (Slack, Confluence, Jira, Salesforce, GitHub, Community Forums) |
| **Users** | All employees, support engineers, customers, community members |

## 3.2 Consolidated Initiatives

| Original Initiative | Owner | Unified Role | Status Integration |
|---------------------|-------|--------------|-------------------|
| Support Intelligence Platform | Hitesh Jha | Core Engine (Multi-source RAG + Agentic AI) | Architecture, becomes foundation |
| Slack-Bot | Tanish/Neha | Slack Interface + Thread Summarization | Completed, integrated |
| Community Bot | Tanish/Shivani | Discourse Forum Interface (External) | In Progress, integrated |
| Cleerfeed Bot | Shivani Moze | Internal Documentation Q&A (Slack) | Completed, integrated |
| ConfluenceGPT | Saurabh Patil | Confluence Search & Chat Interface | Idea, integrated |
| AI Client Req. Extractor | Rohit Vyawahare | Code Generation from Diagnostics (Remediation) | Idea, integrated |
| sf-jira-ai-agent | Neha Marathe | Support Ticket Automation | Idea |
| SF-Jira reporting App | Tanish/Saurabh | SF-Jira Synchronization & Reporting | Idea |

## 3.3 Unified Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                      MULTI-CHANNEL INTERFACE LAYER                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────────┐ │
│  │    Slack     │  │   Web UI     │  │  Discourse   │  │    Salesforce            │ │
│  │    (Bolt)    │  │   (React)    │  │   Forum      │  │    (Cases)               │ │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └───────────┬──────────────┘ │
│         │                 │                 │                      │                │
│  ┌──────┴───────┐  ┌──────┴───────┐  ┌──────┴───────┐  ┌───────────┴──────────────┐ │
│  │  Jira        │  │  Confluence  │  │   GitHub     │  │   API (REST/gRPC)        │ │
│  │  (Tickets)   │  │  (Embedded)  │  │   (Issues)   │  │   (External Systems)     │ │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └───────────┬──────────────┘ │
└─────────┼─────────────────┼─────────────────┼──────────────────────┼────────────────┘
          │                 │                 │                      │
          └─────────────────┴─────────────────┴──────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                      API GATEWAY & ORCHESTRATION                                    │
│                    (FastAPI + LangGraph Agent Orchestration)                        │
│  • Multi-tenant routing  • Authentication/SSO  • Rate Limiting  • Webhook Handlers  │
└─────────────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         AGENTIC AI LAYER                                            │
│                    (LangGraph Multi-Agent Workflows)                                │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌──────────────────────────────┐  ┌─────────────────────────────────────────────┐  │
│  │    QUERY PROCESSING AGENTS   │  │      REASONING & SYNTHESIS AGENTS          │  │
│  │  ┌────────────────────────┐  │  │  ┌──────────────────────────────────────┐  │  │
│  │  │ Query Classifier Agent │  │  │  │ Cross-Source Correlation Agent       │  │  │
│  │  │ • Jira ID detection    │  │  │  │ • Entity matching                    │  │  │
│  │  │ • SF case detection    │  │  │  │ • Error signature similarity         │  │  │
│  │  │ • NL classification    │  │  │  │ • Root cause grouping                │  │  │
│  │  │ • Intent recognition   │  │  │  │ • Pattern clustering                 │  │  │
│  │  └────────────────────────┘  │  │  └──────────────────────────────────────┘  │  │
│  │  ┌────────────────────────┐  │  │  ┌──────────────────────────────────────┐  │  │
│  │  │ Retrieval Agent        │  │  │  │ Resolution Synthesis Agent           │  │  │
│  │  │ • Hybrid search        │  │  │  │ • Answer generation                  │  │  │
│  │  │ • Metadata filtering   │  │  │  │ • Step-by-step resolution            │  │  │
│  │  │ • Reranking            │  │  │  │ • Related cases linking              │  │  │
│  │  │ • Source aggregation   │  │  │  │ • Confidence scoring                 │  │  │
│  │  └────────────────────────┘  │  │  └──────────────────────────────────────┘  │  │
│  │  ┌────────────────────────┐  │  │  ┌──────────────────────────────────────┐  │  │
│  │  │ Summarization Agent    │  │  │  │ Validation Agent                     │  │  │
│  │  │ • Thread summarization │  │  │  │ • Groundedness check                 │  │  │
│  │  │ • Multi-style output   │  │  │  │ • Hallucination detection            │  │  │
│  │  │ • Decision extraction  │  │  │  │ • Citation verification              │  │  │
│  │  │ • Action item tagging  │  │  │  │ • Confidence threshold               │  │  │
│  │  └────────────────────────┘  │  │  └──────────────────────────────────────┘  │  │
│  └──────────────────────────────┘  └─────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌──────────────────────────────┐  ┌─────────────────────────────────────────────┐  │
│  │    PROACTIVE AGENTS          │  │      REMEDIATION AGENTS                    │  │
│  │  ┌────────────────────────┐  │  │  ┌──────────────────────────────────────┐  │  │
│  │  │ Pattern Monitor Agent  │  │  │  │ Auto-Resolution Agent                │  │  │
│  │  │ • Webhook listeners    │  │  │  │ • Suggested reply generation         │  │  │
│  │  │ • Similarity search    │  │  │  │ • Template matching                  │  │  │
│  │  │ • Slack notifications  │  │  │  │ • Draft creation                     │  │  │
│  │  │ • Risk alerts          │  │  │  │ • Engineer assistance                │  │  │
│  │  └────────────────────────┘  │  │  └──────────────────────────────────────┘  │  │
│  │  ┌────────────────────────┐  │  │  ┌──────────────────────────────────────┐  │  │
│  │  │ Escalation Agent       │  │  │  │ Ticket Creation Agent                │  │  │
│  │  │ • SLA risk prediction  │  │  │  │ • Auto-Jira creation                 │  │  │
│  │  │ • Severity assessment  │  │  │  │ • SF case linking                    │  │  │
│  │  │ • Routing decisions    │  │  │  │ • Context preservation               │  │  │
│  │  └────────────────────────┘  │  │  └──────────────────────────────────────┘  │  │
│  └──────────────────────────────┘  └─────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                      RETRIEVAL & KNOWLEDGE LAYER                                    │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    HYBRID SEARCH ENGINE                                       │  │
│  │  ┌─────────────────────┐  ┌─────────────────────┐  ┌─────────────────────┐   │  │
│  │  │   DENSE SEARCH      │  │   SPARSE SEARCH     │  │   CROSS-ENCODER     │   │  │
│  │  │   (Vector)          │  │   (BM25/Keyword)    │  │   RERANKER          │   │  │
│  │  │                     │  │                     │  │                     │   │  │
│  │  │ • BGE-large/E5      │  │ • OpenSearch BM25   │  │ • Relevance scoring │   │  │
│  │  │ • Semantic sim.     │  │ • Term matching     │  │ • Result ranking    │   │  │
│  │  │ • Context aware     │  │ • Exact matches     │  │ • Top-k selection   │   │  │
│  │  └─────────────────────┘  └─────────────────────┘  └─────────────────────┘   │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    VECTOR DATABASE (OpenSearch/Qdrant)                        │  │
│  │                                                                               │  │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────┐  │  │
│  │  │  Confluence │ │    Jira     │ │    Slack    │ │   GitHub    │ │   SF    │  │  │
│  │  │    Docs     │ │  Tickets    │ │  Messages   │ │   Code/PRs  │ │  Cases  │  │  │
│  │  │             │ │             │ │             │ │             │ │         │  │  │
│  │  │ • Pages     │ │ • Issues    │ │ • Threads   │ │ • Functions │ │ • Cases │  │  │
│  │  │ • Spaces    │ │ • Comments  │ │ • Channels  │ │ • PRs       │ │ • Notes │  │  │
│  │  │ • KB        │ │ • History   │ │ • DMs       │ │ • Issues    │ │ • Res.  │  │  │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘ └─────────┘  │  │
│  │                                                                               │  │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────────────────┐  │  │
│  │  │  Community  │ │    MDX      │ │   Product   │ │      Discourse          │  │  │
│  │  │   Forums    │ │    Docs     │ │     Docs    │ │       Forum             │  │  │
│  │  │             │ │             │ │             │ │                         │  │  │
│  │  │ • Posts     │ │ • Articles  │ │ • Guides    │ │ • Topics                │  │  │
│  │  │ • Replies   │ │ • API Ref   │ │ • Tutorials │ │ • Replies               │  │  │
│  │  │ • Solutions │ │ • Examples  │ │ • FAQs      │ │ • Solutions             │  │  │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────────────────┘  │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    GRAPH CORRELATION (Neo4j Optional)                         │  │
│  │  • Cross-source entity relationships                                            │  │
│  │  • Issue-to-solution path tracing                                               │  │
│  │  • Knowledge graph for complex correlations                                     │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                      DATA INGESTION PIPELINE                                        │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌─────────────────────────┐  │
│  │   Webhooks   │  │    APIs      │  │   Crawlers   │  │    Manual Upload        │  │
│  │              │  │              │  │              │  │                         │  │
│  │ • Jira       │  │ • Confluence │  │ • GitHub     │  │ • Documents             │  │
│  │ • Salesforce │  │ • Slack      │  │ • Forums     │  │ • Markdown              │  │
│  │ • GitHub     │  │ • SF REST    │  │ • Websites   │  │ • Code snippets         │  │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └───────────┬─────────────┘  │
│         │                 │                 │                      │                │
│         └─────────────────┴─────────────────┴──────────────────────┘                │
│                                    │                                                │
│                                    ▼                                                │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    PROCESSING PIPELINE                                        │  │
│  │  ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐    │  │
│  │  │ Extract  │──▶│  Chunk   │──▶│ Embed    │──▶│  Index   │──▶│  Store   │    │  │
│  │  │          │   │          │   │          │   │          │   │          │    │  │
│  │  │ • Text   │   │ • Smart  │   │ • BGE    │   │ • Dense  │   │ • Vector │    │  │
│  │  │ • Code   │   │   split  │   │ • E5     │   │ • Sparse │   │ • Graph  │    │  │
│  │  │ • Meta   │   │ • Overlap│   │          │   │ • BM25   │   │ • Cache  │    │  │
│  │  └──────────┘   └──────────┘   └──────────┘   └──────────┘   └──────────┘    │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

## 3.4 Unified Tech Stack

| Component | Technology | Justification |
|-----------|------------|---------------|
| **API Framework** | FastAPI | Already selected for Support Intel |
| **Agent Orchestration** | LangGraph | Multi-agent workflows, Hitesh's preference |
| **LLM Gateway** | SambaNova API | Internal LLM, consistent across tools |
| **Embedding Models** | BGE-large, E5-large | Hitesh's recommendation |
| **Vector DB** | OpenSearch | Hybrid search (vector + BM25) |
| **Graph DB** | Neo4j (optional) | Cross-source correlation |
| **Cache** | Redis | Embedding cache, LLM response cache |
| **Web UI** | React | Support Intel already uses it |
| **Slack Bot** | Slack Bolt SDK | All Slack bots use this |
| **Message Queue** | Redis/RabbitMQ | Webhook processing |

## 3.5 Key Features & Capabilities

### Multi-Source Unified Retrieval

**Supported Sources:**

| Source Type | Examples | Content Types |
|-------------|----------|---------------|
| **Internal** | Confluence, Jira, Slack, GitHub, Salesforce | Pages, tickets, messages, code, cases |
| **External** | Community Forums, Public Docs, Stack Overflow | Posts, articles, solutions |

**Search Capabilities:**
- Hybrid Search (Vector + BM25)
- Metadata Filtering
- Cross-Source Indexing
- Reranking
- Citation Enforcement

### Intelligent Query Processing

**Query Type Detection:**

| Query Type | Detection Method | Example |
|------------|------------------|---------|
| Jira ID | Pattern: PROJ-1234 | "What is the status of SUPPORT-1234?" |
| Salesforce Case | Pattern: 00012345 | "Find case 00012345" |
| Slack Thread | Context + Thread reference | "Summarize this thread" |
| Natural Language | Intent classification | "How do I fix OOMKilled?" |
| Code Query | Language detection | "Show me Python examples for K8s client" |

### Multi-Style Summarization (Slack-Bot Integration)

| Style | Use Case | Output Format |
|-------|----------|---------------|
| **JIRA-style** | Engineering task tracking | Key decisions, action items, assignees, due dates |
| **Productboard-style** | Product feedback & roadmap | User feedback, feature requests, pain points, priorities |
| **Incident Report** | On-call & outage updates | Timeline, impact assessment, resolution steps, follow-up actions |
| **Executive Summary** | Leadership updates | Key points, decisions made, next steps |

### Cross-Source Correlation & Analysis

| Method | Description | Example |
|--------|-------------|---------|
| **Entity Matching** | Link same entities across sources | "Issue in Jira matches Slack thread and SF case" |
| **Error Signature Similarity** | Match similar error patterns | "This OOM error matches 5 past cases" |
| **Embedding-based Clustering** | Cluster similar issues | "These 3 tickets are related" |
| **Root Cause Grouping** | Group by common root causes | "All caused by memory leak" |

### Proactive Monitoring & Alerts

| Capability | Trigger | Action |
|------------|---------|--------|
| **New Case Similarity** | New Jira/SF case created | Auto-search similar cases, post to Slack |
| **Recurring Pattern Detection** | 3+ similar issues in 24h | Alert channel, suggest escalation |
| **SLA Risk Prediction** | Case approaching SLA threshold | Notify assignee + manager |
| **Knowledge Gap Detection** | No results for query | Flag for documentation team |

### Structured Response Generation

| Component | Description | Example |
|-----------|-------------|---------|
| **Summary** | Brief answer to query | "This issue is caused by..." |
| **Root Cause** | Underlying cause analysis | "Memory leak in module X" |
| **Resolution Steps** | Step-by-step fix instructions | "1. Check logs 2. Restart pod..." |
| **Related Cases** | Links to similar issues | "See also: JIRA-123, SF-456" |
| **References** | Source citations | "[Confluence: XRDU Troubleshooting]" |
| **Confidence Score** | Reliability indicator | "Confidence: 95%" |

---

# PART 4: SHARED COMPONENTS & CROSS-SYSTEM INTEGRATION

## 4.1 Shared Infrastructure

Both unified systems share the following infrastructure:

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                         SHARED INFRASTRUCTURE LAYER                                 │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  ┌───────────────────────────────────────────────────────────────────────────────┐  │
│  │                    SHARED SERVICES                                             │  │
│  │                                                                               │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │  │
│  │  │  LLM        │  │  Vector DB  │  │   Cache     │  │   Monitoring        │  │  │
│  │  │  Gateway    │  │  (OpenSearch│  │  (Redis)    │  │   (Prometheus/      │  │  │
│  │  │             │  │   /Qdrant)  │  │             │  │    Grafana)         │  │  │
│  │  │ • SambaNova │  │             │  │ • Sessions  │  │                     │  │  │
│  │  │ • Fallback  │  │ • Embeddings│  │ • Results   │  │ • Metrics           │  │  │
│  │  │ • Routing   │  │ • Metadata  │  │ • LLM       │  │ • Alerting          │  │  │
│  │  │ • Rate limit│  │ • BM25      │  │ • Embeddings│  │ • Dashboards        │  │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────────┘  │  │
│  │                                                                               │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │  │
│  │  │  Embedding  │  │   Object    │  │  Relational │  │   Message Queue     │  │  │
│  │  │  Service    │  │  Storage    │  │     DB      │  │                     │  │  │
│  │  │             │  │             │  │             │  │ • Webhook           │  │  │
│  │  │ • BGE-large │  │ • S3/MinIO  │  │ • PostgreSQL│  │   processing        │  │  │
│  │  │ • E5-large  │  │ • Logs      │  │ • Metadata  │  │ • Async tasks       │  │  │
│  │  │ • Caching   │  │ • Reports   │  │ • Audit     │  │ • Event streaming   │  │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────────┘  │  │
│  │                                                                               │  │
│  └───────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

## 4.2 Cross-System Integration Points

| Integration | IDIP → UKSIP | UKSIP → IDIP |
|-------------|--------------|--------------|
| **Knowledge Retrieval** | Diagnostics query knowledge base for troubleshooting docs | Support queries infrastructure status |
| **Error Correlation** | Infrastructure errors linked to known issues in KB | Past solutions applied to current diagnostics |
| **Proactive Alerts** | Infrastructure alerts trigger knowledge search | Recurring issues trigger diagnostic runs |
| **Documentation** | Auto-generate docs from diagnostic findings | Use existing docs for error explanations |
| **Code Generation** | Generate remediation code from diagnostics | Use code patterns from KB for fixes |

---



# PART 5: TECHNOLOGY CONSOLIDATION MATRIX

## 5.1 Before vs After

| Component | Before (10 systems) | After (2 systems) | Savings |
|-----------|--------------------|--------------------|---------|
| **Vector Databases** | 4 (FAISS x2, OpenSearch, Custom) | 1 (OpenSearch) | 75% |
| **LLM Integrations** | 5 (SambaNova x2, OpenAI, Copilot, Gemini) | 1 (SambaNova Gateway) | 80% |
| **Slack Bots** | 3 separate bots | 1 unified bot | 67% |
| **Web UIs** | 3 (React x2, Custom) | 1 (React) | 67% |
| **CLI Tools** | 2 (Typer, Custom) | 1 (Typer) | 50% |
| **Embedding Pipelines** | 4 | 1 | 75% |
| **API Frameworks** | 2 (FastAPI, Flask) | 1 (FastAPI) | 50% |
| **Cache Layers** | 2 (Redis x2) | 1 (Redis) | 50% |

## 5.2 Unified Technology Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| **API Gateway** | FastAPI | Unified API layer |
| **Agent Framework** | LangGraph | Multi-agent orchestration |
| **LLM** | SambaNova | Internal LLM gateway |
| **Embeddings** | BGE-large, E5-large | Text embeddings |
| **Vector DB** | OpenSearch | Hybrid search |
| **Cache** | Redis | Session & result caching |
| **Web UI** | React | User interface |
| **CLI** | Typer | Command-line interface |
| **Monitoring** | Prometheus/Grafana | Observability |
| **Message Queue** | Redis/RabbitMQ | Async processing |

---

# PART 6: SUCCESS METRICS

## 6.1 System Performance Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| **Query Response Time** | < 2 seconds | Average response time |
| **System Uptime** | > 99.9% | Availability monitoring |
| **Concurrent Users** | 500+ | Load testing |
| **Ingestion Rate** | 1000 docs/hour | Processing throughput |

## 6.2 Business Impact Metrics

| Metric | Target | Source |
|--------|--------|--------|
| **MTTR Reduction** | 50-70% | Support ticket analysis |
| **Search Time Reduction** | 70% | User surveys |
| **First-Response Accuracy** | 90%+ | Feedback scoring |
| **Knowledge Reuse** | 60%+ | Related case clicks |
| **Documentation Coverage** | 95%+ | Gap analysis |

## 6.3 Adoption Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| **Daily Active Users** | 200+ | Analytics |
| **Queries per Day** | 1000+ | API metrics |
| **Slack Bot Interactions** | 500+/day | Slack analytics |
| **Documentation Contributions** | 50+/month | Git metrics |

---

# APPENDIX A: MIGRATION STRATEGY

## A.1 Existing Tool Migration

| Tool | Migration Strategy | Timeline |
|------|-------------------|----------|
| **XRDU Diagnostic** | Port to IDIP as module | Phase 1 |
| **Sambastack Inspector** | Continue development within IDIP | Phase 1-2 |
| **Slack-Bot** | Migrate to UKSIP Slack interface | Phase 1 |
| **Community Bot** | Port to UKSIP as forum interface | Phase 2 |
| **Cleerfeed Bot** | Merge into UKSIP Slack bot | Phase 1 |
| **Support Intel** | Use as UKSIP foundation | Phase 1 |

## A.2 Data Migration

| Data Source | Migration Approach |
|-------------|-------------------|
| **FAISS indices** | Re-index into OpenSearch |
| **Existing embeddings** | Reuse if compatible, else regenerate |
| **Configuration** | Migrate to unified config system |
| **User data** | Preserve and migrate |

## A.3 Backward Compatibility

| Aspect | Strategy |
|--------|----------|
| **API compatibility** | Maintain v1 APIs during transition |
| **CLI commands** | Provide migration guide, keep aliases |
| **Slack commands** | Gradual rollout, keep old bot running |
| **Web UI** | Redirect to new unified portal |

---

# CONCLUSION

This unified architecture consolidates 10 separate initiatives into 2 powerful, integrated platforms:

1. **InfraAI (IDIP)**: Unified infrastructure diagnostics combining hardware monitoring, Kubernetes troubleshooting, and AI-powered remediation.

2. **KnowledgeAI (UKSIP)**: Unified knowledge and support platform combining multi-source retrieval, intelligent assistance, and proactive monitoring.

**Key Benefits:**
- 60-75% reduction in redundant infrastructure
- Consistent user experience across all channels
- Cross-system intelligence and data sharing
- Faster development through component reuse
- Reduced maintenance overhead
- Lower operational costs

**Next Steps:**
1. Review and approve architecture
2. Assign ownership for shared infrastructure
3. Begin Phase 1 implementation
4. Establish weekly sync between system owners
5. Create detailed API specifications

---



**Reviewers:**
- All initiative owners
- Platform team
- Security team

---
