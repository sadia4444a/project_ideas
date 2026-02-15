# TrustTwin — Technical Architecture Diagrams

**Document Version:** 1.0  
**Date:** February 15, 2026  
**Purpose:** Visual architecture documentation for engineering and investors

---

## Table of Contents

1. [High-Level System Architecture](#high-level-architecture)
2. [Data Flow Diagram](#data-flow)
3. [Component Architecture](#component-architecture)
4. [Decision Processing Pipeline](#decision-pipeline)
5. [Knowledge Graph Structure](#knowledge-graph)
6. [Explanation Engine Flow](#explanation-engine)
7. [Simulation Engine Architecture](#simulation-engine)
8. [Security & Privacy Layers](#security-layers)
9. [Deployment Architecture](#deployment-architecture)
10. [Integration Patterns](#integration-patterns)
11. [Microservices Architecture](#microservices)
12. [Database Schema Overview](#database-schema)

---

<a name="high-level-architecture"></a>

## 1. High-Level System Architecture

```mermaid
graph TB
    subgraph "User Layer"
        UI[Web Dashboard]
        Mobile[Mobile App]
        CLI[CLI Tools]
        SDK[Client SDKs<br/>Python, Java, Node]
    end

    subgraph "API Gateway Layer"
        Gateway[API Gateway<br/>Authentication, Rate Limiting]
        Auth[Auth Service<br/>OAuth2, SSO]
    end

    subgraph "Core Services Layer"
        Decision[Decision Service]
        Explain[Explanation Engine]
        Simulate[Simulation Engine]
        Causal[Causal Reasoning]
        Audit[Audit Service]
        Alert[Alert & Monitoring]
    end

    subgraph "Integration Layer"
        Connectors[Connector Framework]
        ETL[ETL Pipeline]
        Queue[Message Queue<br/>Kafka]
    end

    subgraph "Data Layer"
        KG[(Knowledge Graph<br/>Neo4j)]
        DB[(Decision Storage<br/>PostgreSQL)]
        Ledger[(Verified Ledger<br/>Blockchain)]
        Cache[(Cache<br/>Redis)]
        Storage[(Object Storage<br/>S3)]
        Metrics[(Time-Series DB<br/>InfluxDB)]
    end

    subgraph "External Systems"
        Customer[Customer Systems<br/>Databases, APIs]
        ML[ML Platforms<br/>SageMaker, Vertex]
        LLM[LLM APIs<br/>OpenAI, Anthropic]
        Regulatory[Regulatory APIs<br/>Credit Bureaus]
    end

    UI --> Gateway
    Mobile --> Gateway
    CLI --> Gateway
    SDK --> Gateway

    Gateway --> Auth
    Gateway --> Decision
    Gateway --> Explain
    Gateway --> Simulate
    Gateway --> Audit

    Decision --> KG
    Decision --> DB
    Decision --> Ledger
    Decision --> Cache

    Explain --> LLM
    Explain --> KG
    Explain --> Causal

    Simulate --> Causal
    Simulate --> KG
    Simulate --> DB

    Audit --> DB
    Audit --> Ledger
    Audit --> Storage

    Alert --> Metrics
    Alert --> Decision

    Connectors --> ETL
    ETL --> Queue
    Queue --> Decision

    Customer --> Connectors
    ML --> Connectors
    Regulatory --> Connectors

    style Decision fill:#4A90E2
    style Explain fill:#4A90E2
    style Simulate fill:#4A90E2
    style KG fill:#50C878
    style DB fill:#50C878
    style Ledger fill:#F5A623
```

**Key Components:**

- **User Layer:** Multiple interfaces for different personas (developers, compliance teams, auditors)
- **API Gateway:** Central entry point with security and rate limiting
- **Core Services:** Business logic microservices
- **Integration Layer:** Connects to customer systems
- **Data Layer:** Multi-model data storage (graph, relational, blockchain, cache)
- **External Systems:** Third-party integrations

---

<a name="data-flow"></a>

## 2. Data Flow Diagram — Decision Logging

```mermaid
sequenceDiagram
    participant Customer as Customer System
    participant Connector as TrustTwin Connector
    participant Queue as Message Queue
    participant Decision as Decision Service
    participant KG as Knowledge Graph
    participant DB as Decision DB
    participant Ledger as Verified Ledger
    participant Explain as Explanation Engine
    participant LLM as LLM API
    participant User as Dashboard User

    Customer->>Connector: Decision Event<br/>(loan approved/denied)
    Connector->>Connector: Validate & Transform
    Connector->>Queue: Publish Event
    Queue->>Decision: Consume Event

    Decision->>KG: Query Decision Context<br/>(rules, models, features)
    KG-->>Decision: Context Retrieved

    Decision->>Decision: Enrich Decision Data

    par Store Decision
        Decision->>DB: Store Decision Record
        Decision->>Ledger: Create Signed Entry
    end

    Decision->>Explain: Request Explanation

    Explain->>KG: Get Policy & Rules
    Explain->>LLM: Generate Human Explanation
    LLM-->>Explain: Explanation Text

    Explain->>DB: Store Explanation
    Explain-->>Decision: Explanation Complete

    Decision-->>Connector: Acknowledgment

    User->>Decision: Query Decision
    Decision->>DB: Fetch Decision + Explanation
    DB-->>Decision: Decision Data
    Decision-->>User: Display in Dashboard
```

**Flow Steps:**

1. Customer system makes a decision (e.g., loan approval)
2. TrustTwin connector captures and validates the event
3. Event queued for processing (ensures no data loss)
4. Decision service enriches data from knowledge graph
5. Parallel storage: relational DB + tamper-proof ledger
6. Explanation engine generates human-readable text
7. User can view complete decision with explanation

---

<a name="component-architecture"></a>

## 3. Component Architecture — Detailed View

```mermaid
graph TB
    subgraph "Presentation Layer"
        WebUI[Web Dashboard<br/>React + TypeScript]
        MobileApp[Mobile App<br/>React Native]
        APIDoc[API Documentation<br/>OpenAPI/Swagger]
    end

    subgraph "API Layer"
        RestAPI[REST API<br/>FastAPI/Python]
        GraphQLAPI[GraphQL API<br/>Optional]
        WebSocket[WebSocket<br/>Real-time Updates]
    end

    subgraph "Business Logic Layer"
        subgraph "Decision Management"
            DecisionSvc[Decision Service]
            PolicyMgr[Policy Manager]
            VersionCtrl[Version Control]
        end

        subgraph "Intelligence Layer"
            ExplainSvc[Explanation Service]
            CausalEngine[Causal Engine]
            SimEngine[Simulation Engine]
            MLWrapper[ML Model Wrapper]
        end

        subgraph "Compliance & Audit"
            AuditSvc[Audit Service]
            ComplianceChk[Compliance Checker]
            ReportGen[Report Generator]
        end

        subgraph "Governance"
            AccessCtrl[Access Control<br/>RBAC]
            AuditLog[Audit Logger]
            Alerting[Alert Manager]
        end
    end

    subgraph "Data Access Layer"
        KGDAL[Knowledge Graph DAL]
        DecisionDAL[Decision DAL]
        LedgerDAL[Ledger DAL]
        CacheDAL[Cache DAL]
    end

    subgraph "Infrastructure Layer"
        subgraph "Data Stores"
            Neo4j[(Neo4j<br/>Knowledge Graph)]
            Postgres[(PostgreSQL<br/>Decisions)]
            LedgerDB[(Ledger<br/>Blockchain)]
            Redis[(Redis Cache)]
            S3[(S3 Storage)]
        end

        subgraph "Message & Events"
            Kafka[Kafka<br/>Event Stream]
            RabbitMQ[RabbitMQ<br/>Task Queue]
        end

        subgraph "External Services"
            OpenAI[OpenAI API]
            Anthropic[Anthropic API]
            CloudServices[AWS/Azure/GCP]
        end
    end

    WebUI --> RestAPI
    MobileApp --> RestAPI
    RestAPI --> DecisionSvc
    RestAPI --> ExplainSvc
    RestAPI --> SimEngine
    RestAPI --> AuditSvc

    DecisionSvc --> PolicyMgr
    DecisionSvc --> VersionCtrl
    DecisionSvc --> KGDAL
    DecisionSvc --> DecisionDAL
    DecisionSvc --> LedgerDAL

    ExplainSvc --> CausalEngine
    ExplainSvc --> MLWrapper
    ExplainSvc --> OpenAI
    ExplainSvc --> Anthropic

    SimEngine --> CausalEngine
    SimEngine --> KGDAL

    AuditSvc --> ComplianceChk
    AuditSvc --> ReportGen

    AccessCtrl --> AuditLog
    Alerting --> Kafka

    KGDAL --> Neo4j
    DecisionDAL --> Postgres
    LedgerDAL --> LedgerDB
    CacheDAL --> Redis

    DecisionSvc --> Kafka

    style DecisionSvc fill:#4A90E2
    style ExplainSvc fill:#4A90E2
    style SimEngine fill:#4A90E2
    style CausalEngine fill:#9013FE
    style Neo4j fill:#50C878
    style Postgres fill:#50C878
    style LedgerDB fill:#F5A623
```

---

<a name="decision-pipeline"></a>

## 4. Decision Processing Pipeline

```mermaid
flowchart LR
    subgraph "Ingestion Stage"
        A[Decision Event] --> B{Validate Schema}
        B -->|Valid| C[Enrich Metadata]
        B -->|Invalid| Z1[Error Queue]
        C --> D[Deduplicate]
        D --> E[Transform Format]
    end

    subgraph "Context Gathering Stage"
        E --> F[Query Knowledge Graph]
        F --> G[Retrieve Business Rules]
        F --> H[Fetch Model Metadata]
        F --> I[Get Historical Context]
        G --> J[Merge Context]
        H --> J
        I --> J
    end

    subgraph "Analysis Stage"
        J --> K[Feature Extraction]
        K --> L[Rule Evaluation]
        L --> M[Model Inference<br/>if needed]
        M --> N[Decision Validation]
        N -->|Valid| O[Continue]
        N -->|Invalid| Z2[Flag for Review]
    end

    subgraph "Storage Stage"
        O --> P[Generate Decision ID]
        P --> Q[Cryptographic Signing]
        Q --> R{Parallel Storage}
        R --> S[PostgreSQL<br/>Full Record]
        R --> T[Ledger<br/>Signed Hash]
        R --> U[Cache<br/>Recent Decisions]
        R --> V[S3<br/>Artifacts]
    end

    subgraph "Explanation Stage"
        S --> W[Generate Explanation]
        W --> X{Explanation Type}
        X -->|Customer| Y1[Simple Template]
        X -->|Auditor| Y2[Detailed Technical]
        X -->|Regulator| Y3[Compliance Report]
        Y1 --> AA[Store Explanation]
        Y2 --> AA
        Y3 --> AA
    end

    subgraph "Post-Processing Stage"
        AA --> AB[Update Metrics]
        AB --> AC[Check Alert Rules]
        AC -->|Triggered| AD[Send Alert]
        AC -->|OK| AE[Complete]
        AD --> AE
    end

    style A fill:#E8F5E9
    style Q fill:#F5A623
    style W fill:#4A90E2
    style AE fill:#66BB6A
    style Z1 fill:#EF5350
    style Z2 fill:#EF5350
```

**Pipeline Stages:**

1. **Ingestion:** Validate, enrich, deduplicate incoming decisions
2. **Context Gathering:** Pull relevant rules, models, historical data from knowledge graph
3. **Analysis:** Extract features, evaluate rules, validate decision logic
4. **Storage:** Store in multiple systems (DB, ledger, cache, storage) in parallel
5. **Explanation:** Generate appropriate explanation for different audiences
6. **Post-Processing:** Update metrics, trigger alerts if needed

**Performance Targets:**

- Ingestion to storage: <100ms (P99)
- Full pipeline completion: <2s (P95)
- Throughput: 10,000+ decisions/second per cluster

---

<a name="knowledge-graph"></a>

## 5. Knowledge Graph Structure

```mermaid
graph LR
    subgraph "Data Sources"
        DS1[Customer Database]
        DS2[Credit Bureau API]
        DS3[Internal Systems]
    end

    subgraph "Features & Attributes"
        F1[Credit Score]
        F2[Income]
        F3[Debt Amount]
        F4[Employment Status]
        F5[Credit Inquiries]
    end

    subgraph "Business Rules"
        R1[Min Credit Score<br/>Rule: >= 650]
        R2[Max Debt Ratio<br/>Rule: <= 40%]
        R3[Employment Check<br/>Rule: Current Job]
    end

    subgraph "ML Models"
        M1[Risk Model v2.3<br/>Random Forest]
        M2[Fraud Model v1.5<br/>XGBoost]
    end

    subgraph "Policies"
        P1[Standard Lending<br/>Policy P-100]
        P2[High-Risk Override<br/>Policy P-150]
    end

    subgraph "Decisions"
        D1[Decision: dec_123<br/>Outcome: Denied<br/>Date: 2026-02-15]
        D2[Decision: dec_124<br/>Outcome: Approved<br/>Date: 2026-02-15]
    end

    subgraph "Actors"
        A1[System: LoanOrigin]
        A2[Human: Risk Manager]
    end

    DS1 -->|provides| F2
    DS2 -->|provides| F1
    DS3 -->|provides| F3

    F1 -->|input_to| R1
    F1 -->|input_to| M1
    F2 -->|input_to| R2
    F3 -->|input_to| R2
    F4 -->|input_to| R3
    F5 -->|affects| F1

    R1 -->|evaluated_in| D1
    R2 -->|evaluated_in| D1
    M1 -->|scored| D1

    P1 -->|contains| R1
    P1 -->|contains| R2
    P1 -->|contains| M1

    P1 -->|applied_to| D1
    P1 -->|applied_to| D2

    A1 -->|executed| D1
    A2 -->|reviewed| D1

    style D1 fill:#4A90E2
    style D2 fill:#4A90E2
    style P1 fill:#9013FE
    style M1 fill:#F5A623
    style R1 fill:#50C878
```

**Graph Node Types:**

- **Data Sources:** Where features come from
- **Features:** Individual data points used in decisions
- **Rules:** Business logic (thresholds, conditions)
- **Models:** ML models with version info
- **Policies:** Collections of rules and models
- **Decisions:** Individual decision records
- **Actors:** Systems or humans involved

**Graph Relationships:**

- `provides`, `input_to`, `affects`, `evaluated_in`, `scored`, `contains`, `applied_to`, `executed`, `reviewed`

**Use Cases:**

- **Lineage tracking:** "Where did this feature value come from?"
- **Impact analysis:** "If I change this rule, what decisions are affected?"
- **Root cause:** "Why did this decision turn out this way?"
- **Simulation:** "What if I change this model version?"

---

<a name="explanation-engine"></a>

## 6. Explanation Engine Flow

```mermaid
flowchart TD
    Start[Decision Logged] --> GetContext[Fetch Decision Context]
    GetContext --> CheckType{Explanation Type?}

    CheckType -->|Customer| CustFlow[Customer Explanation]
    CheckType -->|Auditor| AuditFlow[Auditor Explanation]
    CheckType -->|Regulator| RegFlow[Regulatory Explanation]

    subgraph "Customer Explanation Path"
        CustFlow --> C1[Use Simple Template]
        C1 --> C2[Identify Key Factors<br/>Top 2-3 reasons]
        C2 --> C3[LLM: Generate Plain English<br/>8th grade reading level]
        C3 --> C4[Add Actionable Advice<br/>What if scenarios]
    end

    subgraph "Auditor Explanation Path"
        AuditFlow --> A1[Gather Full Evidence]
        A1 --> A2[List All Rules Evaluated]
        A2 --> A3[Show Model Outputs<br/>with confidence scores]
        A3 --> A4[Generate Feature Importance<br/>SHAP values]
        A4 --> A5[Add Technical Details<br/>versions, timestamps]
    end

    subgraph "Regulatory Explanation Path"
        RegFlow --> R1[Map to Compliance Framework<br/>e.g., Fair Lending]
        R1 --> R2[Check Protected Classes<br/>Bias detection]
        R2 --> R3[Generate Legal Citations<br/>Relevant regulations]
        R3 --> R4[Create Certified Report<br/>with signatures]
    end

    C4 --> Merge[Merge Explanations]
    A5 --> Merge
    R4 --> Merge

    Merge --> Causal{Need Counterfactual?}

    Causal -->|Yes| CF1[Run Causal Analysis]
    CF1 --> CF2[Identify Minimal Changes<br/>for different outcome]
    CF2 --> CF3[Generate What-If Statement]
    CF3 --> Store

    Causal -->|No| Store[Store in Database]

    Store --> Cache[Update Cache]
    Cache --> Return[Return to User]

    style C3 fill:#4A90E2
    style A4 fill:#F5A623
    style R4 fill:#9013FE
    style CF2 fill:#50C878
```

**Explanation Types & Examples:**

**Customer Explanation:**

```
Your loan application was denied because:
1. Your credit score (620) is below our minimum requirement (650)
2. Your debt-to-income ratio (48%) exceeds our maximum (40%)

To improve your chances:
• Pay down $5,000 in debt → approval likely
• Wait 3 months for credit score to recover → approval possible
```

**Auditor Explanation:**

```
Decision ID: dec_abc123
Timestamp: 2026-02-15 10:23:45 UTC
Policy: Standard Lending v2.0

Rules Evaluated:
✗ min_credit_score: 620 < 650 (FAILED)
✗ max_debt_ratio: 48% > 40% (FAILED)
✓ employment_status: employed (PASSED)

Models:
• risk_model_v2.3: score=0.72 (threshold=0.50)
• fraud_model_v1.5: score=0.15 (low risk)

SHAP Feature Importance:
1. credit_score: -0.45 (negative impact)
2. debt_ratio: -0.32 (negative impact)
3. employment: +0.18 (positive impact)
```

**Regulatory Explanation:**

```
FAIR LENDING COMPLIANCE REPORT
Decision: dec_abc123

Protected Class Analysis:
✓ No disparate treatment detected
✓ Adverse action compliant (15 USC § 1691)
✓ Credit reporting notification sent

Basis for Decision:
• Creditworthiness criteria applied uniformly
• No consideration of prohibited factors
• Automated decision validated against policy P-100

Certification: Cryptographic signature attached
Verification: Passed (hash: sha256:abc...)
```

---

<a name="simulation-engine"></a>

## 7. Simulation Engine Architecture

```mermaid
graph TB
    subgraph "Simulation Inputs"
        I1[Historical Decisions<br/>1M records]
        I2[Policy Changes<br/>New rules/thresholds]
        I3[Synthetic Data<br/>Generated scenarios]
        I4[What-If Params<br/>User inputs]
    end

    subgraph "Simulation Types"
        T1[Single Decision<br/>Counterfactual]
        T2[Batch Simulation<br/>Historical replay]
        T3[Policy Testing<br/>A/B comparison]
        T4[Stress Testing<br/>Edge cases]
    end

    subgraph "Processing Engine"
        P1[Load Knowledge Graph<br/>Snapshot]
        P2[Apply Causal Model]
        P3[Execute Decision Logic<br/>in sandbox]
        P4[Calculate Metrics<br/>approval rate, risk]
        P5[Compare Outcomes<br/>before/after]
    end

    subgraph "Analysis Layer"
        A1[Statistical Analysis<br/>significance testing]
        A2[Risk Assessment<br/>default prediction]
        A3[Business Impact<br/>revenue, cost]
        A4[Fairness Metrics<br/>bias detection]
    end

    subgraph "Output & Reporting"
        O1[Outcome Summary<br/>+12% approvals]
        O2[Risk Report<br/>+2% default rate]
        O3[Financial Impact<br/>+$5M revenue]
        O4[Visualizations<br/>charts, graphs]
        O5[Recommendation<br/>proceed/reject]
    end

    I1 --> T2
    I2 --> T3
    I3 --> T4
    I4 --> T1

    T1 --> P1
    T2 --> P1
    T3 --> P1
    T4 --> P1

    P1 --> P2
    P2 --> P3
    P3 --> P4
    P4 --> P5

    P5 --> A1
    P5 --> A2
    P5 --> A3
    P5 --> A4

    A1 --> O1
    A2 --> O2
    A3 --> O3
    A1 --> O4

    O1 --> O5
    O2 --> O5
    O3 --> O5

    style P2 fill:#9013FE
    style P3 fill:#4A90E2
    style O5 fill:#50C878
```

**Simulation Example:**

**Scenario:** "What if we lower minimum credit score from 650 → 640?"

**Process:**

1. Load last 100K loan decisions from database
2. Create knowledge graph snapshot with current policy
3. Apply change: min_credit_score = 640 (instead of 650)
4. Re-run decision logic on all 100K applications
5. Compare outcomes:

```
Before (650 threshold):
• Approvals: 72,000 (72%)
• Denials: 28,000 (28%)
• Expected defaults: 3,600 (5% of approvals)
• Revenue: $50M

After (640 threshold):
• Approvals: 84,000 (84%) ← +12,000 (+16.7%)
• Denials: 16,000 (16%)
• Expected defaults: 5,040 (6% of approvals) ← +1,440 (+40%)
• Revenue: $58M ← +$8M (+16%)

Net Impact:
✓ Revenue increase: +$8M
✗ Default cost increase: +$2M (1,440 × $1,400 avg)
✓ Net benefit: +$6M

Fairness Check:
✓ No disparate impact on protected classes
✓ Approval rate increase uniform across demographics

Recommendation: ✓ PROCEED with policy change
```

---

<a name="security-layers"></a>

## 8. Security & Privacy Layers

```mermaid
graph TB
    subgraph "Network Security"
        N1[WAF<br/>Web Application Firewall]
        N2[DDoS Protection<br/>CloudFlare]
        N3[VPC<br/>Private Network]
        N4[VPN<br/>Enterprise Access]
    end

    subgraph "API Security"
        A1[API Gateway<br/>Rate Limiting]
        A2[OAuth2 / JWT<br/>Token Auth]
        A3[API Key Mgmt<br/>Rotation]
        A4[IP Whitelist<br/>Enterprise only]
    end

    subgraph "Application Security"
        AP1[Input Validation<br/>Schema checks]
        AP2[SQL Injection<br/>Prevention]
        AP3[XSS Protection<br/>Output encoding]
        AP4[CSRF Tokens<br/>State validation]
    end

    subgraph "Access Control"
        AC1[RBAC<br/>Role-Based Access]
        AC2[MFA<br/>Multi-Factor Auth]
        AC3[SSO Integration<br/>Okta, Azure AD]
        AC4[Audit Logging<br/>All access tracked]
    end

    subgraph "Data Security"
        D1[Encryption in Transit<br/>TLS 1.3]
        D2[Encryption at Rest<br/>AES-256]
        D3[Key Management<br/>AWS KMS]
        D4[Data Masking<br/>PII protection]
    end

    subgraph "Privacy Controls"
        P1[PII Detection<br/>Auto-discovery]
        P2[Anonymization<br/>k-anonymity]
        P3[Differential Privacy<br/>Aggregate queries]
        P4[Right to Deletion<br/>GDPR compliance]
    end

    subgraph "Compliance"
        C1[SOC2 Type II<br/>Security audit]
        C2[HIPAA/BAA<br/>Healthcare]
        C3[ISO 27001<br/>InfoSec standard]
        C4[GDPR<br/>Data protection]
    end

    subgraph "Monitoring & Response"
        M1[SIEM<br/>Security monitoring]
        M2[Intrusion Detection<br/>Anomaly alerts]
        M3[Incident Response<br/>Playbooks]
        M4[Penetration Testing<br/>Quarterly]
    end

    N1 --> A1
    N2 --> A1
    N3 --> AP1
    N4 --> AC3

    A1 --> A2
    A2 --> AC1
    A3 --> AC1
    A4 --> AC1

    AP1 --> D1
    AP2 --> D1

    AC1 --> AC4
    AC2 --> AC4

    D1 --> D2
    D2 --> D3
    D3 --> P1

    P1 --> P2
    P2 --> P3
    P3 --> P4

    D4 --> C1
    P4 --> C2

    C1 --> M1
    C2 --> M1
    C3 --> M1
    C4 --> M1

    M1 --> M2
    M2 --> M3

    style D3 fill:#F5A623
    style P3 fill:#9013FE
    style AC1 fill:#4A90E2
    style C1 fill:#50C878
```

**Security Zones:**

```mermaid
graph LR
    subgraph "Internet Zone"
        User[Users<br/>Public Internet]
    end

    subgraph "DMZ - Demilitarized Zone"
        LB[Load Balancer]
        WAF[WAF]
        API[API Gateway]
    end

    subgraph "Application Zone - Private Subnet"
        App1[Decision Service]
        App2[Explanation Service]
        App3[Simulation Service]
    end

    subgraph "Data Zone - Highly Restricted"
        DB1[(Decision DB)]
        DB2[(Knowledge Graph)]
        DB3[(Ledger)]
    end

    subgraph "Management Zone"
        Admin[Admin Portal]
        Monitor[Monitoring]
        Backup[Backup Service]
    end

    User -->|HTTPS| LB
    LB --> WAF
    WAF --> API
    API -->|Internal TLS| App1
    API -->|Internal TLS| App2
    API -->|Internal TLS| App3

    App1 -->|Encrypted| DB1
    App2 -->|Encrypted| DB2
    App3 -->|Encrypted| DB1

    Admin -->|VPN Only| Monitor
    Monitor --> App1
    Backup -->|Encrypted| DB1

    style DMZ fill:#FFE0B2
    style "Application Zone - Private Subnet" fill:#E1F5FE
    style "Data Zone - Highly Restricted" fill:#F3E5F5
```

**Data Privacy Flow:**

```mermaid
flowchart LR
    A[Raw Data<br/>with PII] --> B{PII Detection}
    B --> C[Classify Fields<br/>SSN, email, name]
    C --> D{Purpose?}

    D -->|Storage| E1[Encrypt + Hash<br/>Pseudonymize]
    D -->|Analytics| E2[Anonymize<br/>k-anonymity]
    D -->|Sharing| E3[Differential Privacy<br/>Add noise]
    D -->|Display| E4[Mask<br/>Show last 4 digits]

    E1 --> F1[Secure Storage<br/>AES-256]
    E2 --> F2[Aggregate Reports<br/>No individuals]
    E3 --> F3[Safe Sharing<br/>Partners/Research]
    E4 --> F4[User Interface<br/>Limited exposure]

    F1 --> G{Access Request}
    G -->|Authorized| H[Decrypt with Key<br/>Audit logged]
    G -->|Unauthorized| I[Access Denied<br/>Alert sent]

    style B fill:#F5A623
    style E1 fill:#4A90E2
    style E2 fill:#9013FE
    style H fill:#50C878
    style I fill:#EF5350
```

---

<a name="deployment-architecture"></a>

## 9. Deployment Architecture

```mermaid
graph TB
    subgraph "Multi-Region Deployment"
        subgraph "US-EAST Region - Primary"
            USE1[Load Balancer]
            USE2[API Gateway Cluster<br/>3 nodes]
            USE3[App Services Cluster<br/>10 nodes]
            USE4[PostgreSQL<br/>Primary + 2 replicas]
            USE5[Neo4j Cluster<br/>3 nodes]
            USE6[Redis Cluster<br/>3 nodes]
        end

        subgraph "US-WEST Region - Backup"
            USW1[Load Balancer]
            USW2[API Gateway Cluster<br/>3 nodes]
            USW3[App Services Cluster<br/>10 nodes]
            USW4[PostgreSQL<br/>Read Replica]
            USW5[Neo4j Replica<br/>Read-only]
        end

        subgraph "EU Region - GDPR"
            EU1[Load Balancer]
            EU2[API Gateway Cluster<br/>3 nodes]
            EU3[App Services Cluster<br/>6 nodes]
            EU4[PostgreSQL<br/>Separate instance]
            EU5[Neo4j Cluster<br/>3 nodes]
        end
    end

    subgraph "Global Services"
        CDN[CloudFront CDN<br/>Static assets]
        DNS[Route53 DNS<br/>Geo-routing]
        S3Global[S3 Multi-region<br/>Audit artifacts]
    end

    subgraph "Monitoring & Ops"
        Datadog[Datadog<br/>Metrics & Logs]
        PagerDuty[PagerDuty<br/>Alerts]
        GitHub[GitHub<br/>Code + CI/CD]
    end

    DNS --> USE1
    DNS --> USW1
    DNS --> EU1

    USE1 --> USE2
    USE2 --> USE3
    USE3 --> USE4
    USE3 --> USE5
    USE3 --> USE6

    USW1 --> USW2
    USW2 --> USW3

    EU1 --> EU2
    EU2 --> EU3
    EU3 --> EU4

    USE4 -.Replication.-> USW4
    USE5 -.Replication.-> USW5

    USE3 --> Datadog
    USW3 --> Datadog
    EU3 --> Datadog

    Datadog --> PagerDuty

    GitHub --> USE3
    GitHub --> USW3
    GitHub --> EU3

    style USE4 fill:#50C878
    style EU4 fill:#9013FE
    style Datadog fill:#F5A623
```

**Kubernetes Architecture:**

```mermaid
graph TB
    subgraph "Kubernetes Cluster"
        subgraph "Ingress Layer"
            Ingress[Nginx Ingress<br/>TLS termination]
        end

        subgraph "API Namespace"
            API1[API Gateway Pod]
            API2[API Gateway Pod]
            API3[API Gateway Pod]
            APISvc[API Service<br/>Load Balancer]
        end

        subgraph "Core Services Namespace"
            Dec1[Decision Service<br/>3 replicas]
            Exp1[Explanation Service<br/>3 replicas]
            Sim1[Simulation Service<br/>2 replicas]
            Audit1[Audit Service<br/>2 replicas]
        end

        subgraph "Data Namespace"
            PG[PostgreSQL<br/>StatefulSet]
            Neo[Neo4j<br/>StatefulSet]
            Redis[Redis<br/>StatefulSet]
        end

        subgraph "Jobs Namespace"
            CronJob[Backup CronJob<br/>Daily]
            ETL[ETL Jobs<br/>On-demand]
        end

        subgraph "Monitoring Namespace"
            Prom[Prometheus]
            Graf[Grafana]
            Jaeger[Jaeger Tracing]
        end
    end

    subgraph "External Storage"
        EBS[EBS Volumes<br/>Persistent]
        S3[S3 Buckets<br/>Backups]
    end

    Ingress --> APISvc
    APISvc --> API1
    APISvc --> API2
    APISvc --> API3

    API1 --> Dec1
    API1 --> Exp1
    API1 --> Sim1
    API1 --> Audit1

    Dec1 --> PG
    Dec1 --> Neo
    Dec1 --> Redis

    CronJob --> PG
    CronJob --> S3

    Dec1 --> Prom
    Exp1 --> Prom
    Prom --> Graf

    PG --> EBS
    Neo --> EBS

    style API1 fill:#4A90E2
    style Dec1 fill:#4A90E2
    style PG fill:#50C878
    style Prom fill:#F5A623
```

**Scaling Strategy:**

```mermaid
graph LR
    subgraph "Auto-Scaling Triggers"
        T1[CPU > 70%]
        T2[Memory > 80%]
        T3[Request Queue > 1000]
        T4[Response Time > 2s]
    end

    subgraph "Scaling Actions"
        A1[Horizontal Pod Autoscaler<br/>Add more pods]
        A2[Cluster Autoscaler<br/>Add more nodes]
        A3[Database Scaling<br/>Add read replicas]
    end

    subgraph "Load Distribution"
        L1[Round Robin]
        L2[Least Connections]
        L3[Geo-Routing]
        L4[Sticky Sessions]
    end

    T1 --> A1
    T2 --> A1
    T3 --> A2
    T4 --> A3

    A1 --> L1
    A2 --> L2
    A3 --> L3

    style A1 fill:#4A90E2
    style A2 fill:#50C878
```

---

<a name="integration-patterns"></a>

## 10. Integration Patterns

**Pattern 1: Real-Time Decision Logging**

```mermaid
sequenceDiagram
    participant App as Customer App
    participant SDK as TrustTwin SDK
    participant API as TrustTwin API
    participant Queue as Message Queue
    participant DB as Database

    App->>App: Make Decision<br/>(loan approved)
    App->>SDK: logDecision({<br/>outcome, inputs, models<br/>})
    SDK->>SDK: Validate Schema
    SDK->>API: POST /v1/decisions
    API->>Queue: Publish Event
    API-->>SDK: 202 Accepted<br/>{decision_id}
    SDK-->>App: decision_id returned

    Note over App,SDK: App continues without waiting

    Queue->>DB: Process & Store<br/>(async)
    DB->>Queue: Acknowledgment
```

**Pattern 2: Batch Upload**

```mermaid
sequenceDiagram
    participant Cust as Customer System
    participant SFTP as SFTP Server
    participant ETL as ETL Pipeline
    participant Validate as Validator
    participant TT as TrustTwin

    Cust->>SFTP: Upload CSV<br/>10K decisions
    SFTP->>ETL: File detected
    ETL->>ETL: Parse CSV
    ETL->>Validate: Validate rows
    Validate-->>ETL: 9,950 valid<br/>50 errors
    ETL->>TT: Bulk import<br/>(batches of 1000)
    TT-->>ETL: Processed
    ETL->>Cust: Email report<br/>Success + errors
```

**Pattern 3: Webhook Events**

```mermaid
sequenceDiagram
    participant TT as TrustTwin
    participant Webhook as Webhook Service
    participant Cust as Customer Endpoint

    Note over TT: Decision flagged<br/>for review

    TT->>Webhook: Trigger event<br/>decision.flagged
    Webhook->>Webhook: Retry logic<br/>Max 3 attempts
    Webhook->>Cust: POST /webhook<br/>Decision data
    Cust-->>Webhook: 200 OK
    Webhook->>TT: Delivery confirmed

    alt Delivery Failed
        Webhook->>Cust: Retry #2
        Cust--xWebhook: 500 Error
        Webhook->>Cust: Retry #3
        Cust--xWebhook: Timeout
        Webhook->>TT: Mark as failed<br/>Send alert
    end
```

**Pattern 4: Streaming Integration (Kafka)**

```mermaid
graph LR
    subgraph "Customer Infrastructure"
        App[Application]
        KafkaCust[Kafka Cluster]
    end

    subgraph "TrustTwin"
        Connector[Kafka Connector]
        Consumer[Consumer Group]
        Process[Processing Pipeline]
        Store[(Storage)]
    end

    App -->|Produce| KafkaCust
    KafkaCust -->|Topic: decisions| Connector
    Connector --> Consumer
    Consumer --> Process
    Process --> Store

    style Connector fill:#4A90E2
    style Process fill:#50C878
```

---

<a name="microservices"></a>

## 11. Microservices Architecture

```mermaid
graph TB
    subgraph "Frontend Services"
        WebUI[Web UI Service<br/>React SPA]
        BFF[Backend for Frontend<br/>GraphQL]
    end

    subgraph "Core Business Services"
        DecSvc[Decision Service<br/>Port 8001]
        ExpSvc[Explanation Service<br/>Port 8002]
        SimSvc[Simulation Service<br/>Port 8003]
        AudSvc[Audit Service<br/>Port 8004]
        PolSvc[Policy Service<br/>Port 8005]
    end

    subgraph "Supporting Services"
        AuthSvc[Auth Service<br/>Port 8010]
        NotifSvc[Notification Service<br/>Port 8011]
        SearchSvc[Search Service<br/>Port 8012]
        AnalyticsSvc[Analytics Service<br/>Port 8013]
    end

    subgraph "Data Services"
        KGAPI[Knowledge Graph API<br/>Port 8020]
        LedgerAPI[Ledger API<br/>Port 8021]
        CacheAPI[Cache API<br/>Port 8022]
    end

    subgraph "Integration Services"
        ConnectorSvc[Connector Service<br/>Port 8030]
        ETLSvc[ETL Service<br/>Port 8031]
        WebhookSvc[Webhook Service<br/>Port 8032]
    end

    subgraph "Infrastructure Services"
        Gateway[API Gateway<br/>Port 443]
        ServiceMesh[Istio Service Mesh]
        Discovery[Service Discovery<br/>Consul]
    end

    WebUI --> BFF
    BFF --> Gateway
    Gateway --> ServiceMesh

    ServiceMesh --> DecSvc
    ServiceMesh --> ExpSvc
    ServiceMesh --> SimSvc
    ServiceMesh --> AudSvc
    ServiceMesh --> PolSvc

    DecSvc --> AuthSvc
    DecSvc --> KGAPI
    DecSvc --> LedgerAPI
    DecSvc --> NotifSvc

    ExpSvc --> KGAPI
    ExpSvc --> CacheAPI

    SimSvc --> KGAPI
    SimSvc --> DecSvc

    AudSvc --> LedgerAPI
    AudSvc --> SearchSvc

    ConnectorSvc --> ETLSvc
    ETLSvc --> DecSvc

    DecSvc --> Discovery
    ExpSvc --> Discovery
    AudSvc --> Discovery

    style Gateway fill:#F5A623
    style ServiceMesh fill:#9013FE
    style DecSvc fill:#4A90E2
    style KGAPI fill:#50C878
```

**Service Communication:**

```mermaid
graph LR
    subgraph "Synchronous Communication"
        A[Service A] -->|REST/gRPC| B[Service B]
        B -->|Response| A
    end

    subgraph "Asynchronous Communication"
        C[Service C] -->|Publish Event| Queue[Message Queue]
        Queue -->|Subscribe| D[Service D]
        Queue -->|Subscribe| E[Service E]
    end

    subgraph "Service Mesh"
        F[Service F] -->|mTLS| Mesh[Istio]
        Mesh -->|mTLS| G[Service G]
        Mesh -->|Retry, Circuit Breaker| G
    end

    style Queue fill:#F5A623
    style Mesh fill:#9013FE
```

---

<a name="database-schema"></a>

## 12. Database Schema Overview

**PostgreSQL — Decisions Table**

```mermaid
erDiagram
    DECISIONS ||--o{ DECISION_INPUTS : has
    DECISIONS ||--o{ DECISION_RULES : evaluated
    DECISIONS ||--o{ DECISION_MODELS : used
    DECISIONS ||--o{ EXPLANATIONS : has
    DECISIONS ||--|| LEDGER_ENTRIES : signed_in

    DECISIONS {
        uuid id PK
        timestamp created_at
        string type
        string outcome
        uuid customer_id FK
        uuid policy_id FK
        jsonb metadata
        string version
    }

    DECISION_INPUTS {
        uuid id PK
        uuid decision_id FK
        string feature_name
        variant feature_value
        string source
        timestamp retrieved_at
    }

    DECISION_RULES {
        uuid id PK
        uuid decision_id FK
        string rule_name
        boolean passed
        jsonb details
    }

    DECISION_MODELS {
        uuid id PK
        uuid decision_id FK
        string model_name
        string model_version
        float output_score
        jsonb confidence
    }

    EXPLANATIONS {
        uuid id PK
        uuid decision_id FK
        string type
        text explanation_text
        jsonb counterfactuals
        timestamp generated_at
    }

    LEDGER_ENTRIES {
        uuid id PK
        uuid decision_id FK
        string hash
        string signature
        timestamp signed_at
        string previous_hash
    }

    POLICIES {
        uuid id PK
        string name
        string version
        jsonb rules
        timestamp effective_date
    }

    CUSTOMERS {
        uuid id PK
        string external_id
        jsonb metadata
        timestamp created_at
    }

    DECISIONS }o--|| POLICIES : follows
    DECISIONS }o--|| CUSTOMERS : belongs_to
```

**Neo4j — Knowledge Graph Schema**

```mermaid
graph LR
    subgraph "Node Types"
        N1[DataSource]
        N2[Feature]
        N3[Rule]
        N4[Model]
        N5[Policy]
        N6[Decision]
        N7[Actor]
    end

    subgraph "Relationships"
        N1 -->|PROVIDES| N2
        N2 -->|INPUT_TO| N3
        N2 -->|INPUT_TO| N4
        N3 -->|PART_OF| N5
        N4 -->|PART_OF| N5
        N5 -->|APPLIED_TO| N6
        N3 -->|EVALUATED_IN| N6
        N4 -->|SCORED| N6
        N7 -->|EXECUTED| N6
    end

    style N1 fill:#E3F2FD
    style N2 fill:#FFF3E0
    style N3 fill:#E8F5E9
    style N4 fill:#F3E5F5
    style N5 fill:#FCE4EC
    style N6 fill:#4A90E2
    style N7 fill:#FFE0B2
```

---

## Summary

These diagrams provide a comprehensive visual overview of TrustTwin's technical architecture:

1. **System Architecture:** High-level view of all components and their interactions
2. **Data Flow:** How decisions flow through the system from capture to storage
3. **Component Architecture:** Detailed service breakdown and responsibilities
4. **Decision Pipeline:** Step-by-step processing of decisions
5. **Knowledge Graph:** Structure and relationships for decision context
6. **Explanation Engine:** Multi-path explanation generation
7. **Simulation Engine:** What-if analysis and policy testing
8. **Security Layers:** Multi-layer defense and privacy controls
9. **Deployment:** Multi-region, Kubernetes-based infrastructure
10. **Integration Patterns:** How customers connect to TrustTwin
11. **Microservices:** Service decomposition and communication
12. **Database Schema:** Data model for relational and graph databases

**For Engineering Teams:**

- Use these diagrams as reference during implementation
- Each diagram maps to specific sprint deliverables
- Architecture supports horizontal scaling and high availability

**For Investors:**

- Demonstrates technical sophistication and feasibility
- Shows scalability and enterprise-readiness
- Highlights security and compliance built-in from day one

---

**Next Steps:**

- Export diagrams to PNG/SVG for presentations
- Update as architecture evolves
- Use as onboarding materials for new engineers

**Tools for Rendering:**

- Mermaid Live Editor: https://mermaid.live
- VS Code Mermaid Extension
- GitHub/GitLab (native Mermaid support)
- Markdown viewers (most support Mermaid)

---

_Document Version 1.0 | February 15, 2026_
