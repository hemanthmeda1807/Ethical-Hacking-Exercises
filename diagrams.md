# Technical Diagrams: Behaviour-Aware Honeypot

This document contains the source code for 8 technical diagrams representing the architecture, data flow, and logic of the Behaviour-Aware Honeypot project. You can copy these code blocks into any Mermaid-compatible tool or use a [StarUML Mermaid Plugin](https://github.com/staruml/mermaid) to import them directly.

---

## 1. Architecture Diagram
Shows the high-level physical and logical components of the system.

```mermaid
graph TD
    A[Public Internet] -->|Traffic| B(Cloudflare Tunnel)
    B -->|Encrypted Protocol| C{Docker Network}
    
    subgraph Deception Layer
        C --> D[HTTP Trap Container]
        C --> E[SSH Trap Container]
    end
    
    D -->|REST Telemetry| F[Core Event Bus]
    E -->|Socket Logs| F
    
    subgraph Intelligence Layer
        F --> G[Fast-Path ML Classifier]
        G --> H[Deep-Semantic Intent Engine]
        H --> I[Adaptive Response Engine]
    end
    
    I -->|Orchestration Commands| J[Docker Controller]
    J -->|provision/rotate| C
    
    F -->|Live Stream| K[SOC Dashboard]
    I -->|Alert Trigger| K
```

---

## 2. DFD Level 1 (Context Diagram)
Defines the external entities and the primary system boundary.

```mermaid
graph LR
    User((Attacker)) -->|exploits/payloads| System[Honeypot Core System]
    System -->|deceptive responses| User
    
    Admin((Security Admin)) -->|control commands| System
    System -->|real-time alerts| Admin
    System -->|threat intelligence| Admin
```

---

## 3. DFD Level 2 (Detailed Data Flow)
Details the internal data processing from ingestion to storage.

```mermaid
graph TD
    subgraph Ingestion
        A[Raw Input] -->|Protocol Logs| B[Telemetry Normalizer]
    end
    
    subgraph Analysis
        B -->|Normalized JSON| C[ML Intent Classifier]
        C -->|Risk Scores| D[Behaviour Pipeline]
    end
    
    subgraph Action
        D -->|State Change| E[Response Engine]
        E -->|Countermeasure| F[Protocol Trap]
    end
    
    subgraph Persistence
        D -->|Enriched Event| G[Forensic Database]
        G -->|SHA-256 Hash| H[Tamper-Evident Index]
    end
```

---

## 4. Use Case Diagram
Maps actors to system functions.

```mermaid
useCaseDiagram
    actor Attacker
    actor "Security Analyst" as Analyst
    
    package HoneypotSystem {
        usecase "Interact with SSH/HTTP Traps" as UC1
        usecase "Trigger Deceptive Payloads" as UC2
        usecase "Analyse Real-time Telemetry" as UC3
        usecase "Orchestrate Docker Containers" as UC4
        usecase "Review Threat Reports" as UC5
    }
    
    Attacker --> UC1
    Attacker --> UC2
    Analyst --> UC3
    Analyst --> UC4
    Analyst --> UC5
```

---

## 5. Sequence Diagram
Illustrates the chronological order of messages during an attack.

```mermaid
sequenceDiagram
    participant Attacker
    participant Trap as SSH/HTTP Container
    participant Core as Core Ingestion API
    participant ML as Intent Classifier
    participant Engine as Behaviour Engine
    participant Dash as Streamlit Dashboard

    Attacker->>Trap: Executes malicious payload
    Trap->>Core: POST /event (raw telemetry)
    Core->>ML: classify(payload)
    ML-->>Core: {intent: SQLi, confidence: 0.94}
    Core->>Engine: process_behaviour(event)
    Engine->>Engine: update_state(MALICIOUS)
    Engine-->>Core: {action: DECEIVE, response: fake_db}
    Core->>Trap: Inject Deceptive Payload
    Trap-->>Attacker: Status 200 (Mock Dataset)
    Core->>Dash: Push Live update
```

---

## 6. Class Diagram
Defines the structural relationships of the Python implementation.

```mermaid
classDiagram
    class Orchestrator {
        +start_http()
        +start_ssh()
        +stop_container()
        -network_id: String
    }
    class EventBus {
        +ingest_event(json)
        +route_telemetry()
    }
    class Trap {
        <<interface>>
        +handle_request()
        +send_logs()
    }
    class MLClassifier {
        +classify(input)
        +extract_features()
    }
    class BehaviourEngine {
        +process_state()
        +select_response()
    }

    Orchestrator --> Trap : controls
    Trap --> EventBus : sends logs
    EventBus --> MLClassifier : requests analysis
    MLClassifier --> BehaviourEngine : provides scores
    BehaviourEngine --> Orchestrator : triggers rotation
```

---

## 7. Activity Diagram
Visualizes the decision-making logic of the Response Engine.

```mermaid
stateDiagram-v2
    [*] --> EventReceived
    EventReceived --> CheckConfidence
    
    state CheckConfidence <<choice>>
    CheckConfidence --> FastPath : Confidence > 0.8
    CheckConfidence --> DeepAnalysis : Confidence < 0.8
    
    DeepAnalysis --> UpdateState
    FastPath --> UpdateState
    
    state UpdateState <<choice>>
    UpdateState --> Allow : ThreatLevel LOW
    UpdateState --> Slowdown : ThreatLevel MEDIUM
    UpdateState --> Deceive : ThreatLevel HIGH
    
    Allow --> [*]
    Slowdown --> [*]
    Deceive --> RotateContainer
    RotateContainer --> [*]
```

---

## 8. System Flowchart
Represents the infinite operational loop of the backend service.

```mermaid
graph TD
    Start([Initialize Daemon]) --> LoadModels[Load ML Models]
    LoadModels --> PrepareNetwork[Setup Docker Network]
    PrepareNetwork --> Listen[Listen on Port 5001]
    
    Listen --> Ingest{Event Received?}
    Ingest -- No --> Listen
    Ingest -- Yes --> Process[Enrich & Classify Event]
    
    Process --> Decide{Decision Made?}
    Decide --> Res[Execute Countermeasure]
    Decide --> Log[Log to Forensic Sink]
    
    Res --> UpdateUI[Update Dashboard]
    Log --> UpdateUI
    UpdateUI --> Listen
```
