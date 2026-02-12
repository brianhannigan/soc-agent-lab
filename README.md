# 🛡️ SOC Agent Lab

Autonomous AI agents for SOC alert triage, detection engineering, and log intelligence.

SOC Agent Lab is an educational project that shows how to design a **Security Operations Center (SOC)** workflow around modular AI agents. The focus is not “chatbot demos,” but practical SOC tasks: triaging alerts, grounding decisions with retrieval (RAG), generating detections, and learning from analyst feedback.

---

## Table of Contents

1. [What You Will Build](#what-you-will-build)
2. [Who This Tutorial Is For](#who-this-tutorial-is-for)
3. [Learning Outcomes](#learning-outcomes)
4. [Architecture at a Glance](#architecture-at-a-glance)
5. [Repository Structure](#repository-structure)
6. [Tutorial Walkthrough](#tutorial-walkthrough)
   - [Step 1: Ingest Alerts](#step-1-ingest-alerts)
   - [Step 2: Create Embeddings](#step-2-create-embeddings)
   - [Step 3: Retrieve Context with Vector Search](#step-3-retrieve-context-with-vector-search)
   - [Step 4: Triage with the SOC Agent](#step-4-triage-with-the-soc-agent)
   - [Step 5: Generate Detection-as-Code](#step-5-generate-detection-as-code)
   - [Step 6: Analyst Copilot via Chat Agent](#step-6-analyst-copilot-via-chat-agent)
   - [Step 7: Close the Loop with Feedback](#step-7-close-the-loop-with-feedback)
7. [Example End-to-End Flow](#example-end-to-end-flow)
8. [Sample Data and Output Contracts](#sample-data-and-output-contracts)
9. [Design Principles](#design-principles)
10. [Future Enhancements](#future-enhancements)
11. [Disclaimer](#disclaimer)
12. [Author](#author)

---

## What You Will Build

You will build a simulated SOC automation pipeline where AI agents:

- ingest and normalize alert data,
- retrieve historical context using embeddings and vector search,
- produce structured triage decisions,
- suggest new detection rules,
- support analysts through chat-based investigation, and
- improve confidence calibration from analyst feedback.

This mirrors the architecture patterns used in modern AI-enabled SOCs.

## Who This Tutorial Is For

This tutorial is designed for:

- SOC analysts exploring AI-assisted triage,
- detection engineers interested in Detection-as-Code,
- security engineers building RAG-based workflows,
- AI engineers looking for a realistic agentic security use case.

## Learning Outcomes

By the end, you should be able to:

1. Explain how RAG reduces hallucination in security workflows.
2. Design structured JSON contracts for agent outputs.
3. Connect triage decisions to detection engineering automation.
4. Build a feedback loop that improves risk and confidence scoring.

---

## Architecture at a Glance

```text
Log Sources
  ↓
Data Ingestion Layer
  ↓
Detection-as-Code Engine
  ↓
Alert Object Store
  ↓
Agent Orchestration Layer
  ↓
RAG Context Retrieval (Vector DB)
  ↓
SOC AI Agents
  ↓
Analyst Interaction
  ↓
Feedback Loop & Learning
```

---

## Repository Structure

```text
soc-agent-lab/
├── ingestion/
│   └── alert_simulator.py
├── embeddings/
│   └── embed_pipeline.py
├── vector_store/
│   └── vectordb.py
├── agents/
│   ├── triage_agent.py
│   ├── detection_agent.py
│   └── chat_agent.py
├── evaluation/
│   └── feedback_loop.py
├── api/
│   └── main.py
├── data/
│   └── sample_alerts.json
├── requirements.txt
└── README.md
```

> Note: This lab emphasizes architecture and workflows. Some components may be simulated or stubbed depending on your implementation stage.

---

## Tutorial Walkthrough

### Step 1: Ingest Alerts

Start by simulating SIEM/XDR-style alerts.

Example alert:

```json
{
  "alert_id": "A-1023",
  "device": "WS-445",
  "event_type": "RemoteInteractiveLogon",
  "timestamp": "2025-11-19T02:13:22Z",
  "source_ip": "185.231.44.12",
  "user": "svc_admin"
}
```

Why this matters:

- It is an off-hours login.
- It comes from a public IP.
- It uses a privileged/service-style account.

These are exactly the signals triage models should weigh.

### Step 2: Create Embeddings

Convert alerts, incidents, and playbook text into vector representations.

Embeddings help the system:

- find similar historical alerts,
- identify common false-positive patterns,
- provide semantic context beyond exact keyword matching.

### Step 3: Retrieve Context with Vector Search

Apply Retrieval-Augmented Generation (RAG):

```text
New Alert → Embed → Search Vector DB → Retrieve Similar Alerts + Playbooks
```

The triage agent should reason using:

- the current alert,
- similar incidents,
- internal SOC procedures,
- relevant threat intelligence notes.

### Step 4: Triage with the SOC Agent

The triage agent returns a **structured** decision:

```json
{
  "risk_score": 87,
  "confidence": 0.91,
  "recommended_action": "Escalate to Tier 2",
  "reasoning_summary": "Off-hours admin logon from foreign IP with no prior baseline behavior"
}
```

Key implementation guidance:

- keep output deterministic,
- enforce schema validation,
- separate confidence from severity,
- include concise reasoning for analyst trust.

### Step 5: Generate Detection-as-Code

Use alert patterns to propose new detections.

Example KQL output:

```kql
DeviceLogonEvents
| where LogonType == "RemoteInteractive"
| where RemoteIPType == "Public"
| where Timestamp between (ago(7d) .. now())
| summarize count() by DeviceName
```

This enables faster detection engineering and tighter feedback from operations to content creation.

### Step 6: Analyst Copilot via Chat Agent

The chat agent supports natural-language investigation.

Example analyst prompt:

> Show me all off-hours interactive logons from public IP addresses in the past 7 days.

Typical capabilities:

- explain incident context,
- summarize likely root cause,
- help refine detection logic,
- accelerate hypothesis-driven hunting.

### Step 7: Close the Loop with Feedback

Capture analyst outcomes and feed them back into the system:

- false-positive labels,
- escalation overrides,
- confidence disagreements,
- final case disposition.

Use this feedback to recalibrate:

- risk scoring weights,
- confidence thresholds,
- prompt and retrieval strategies.

---

## Example End-to-End Flow

1. A suspicious off-hours admin login alert is generated.
2. The alert is embedded and sent to the vector database.
3. Similar historical incidents are retrieved.
4. The triage agent assigns risk and confidence.
5. The detection agent proposes a new detection rule.
6. An analyst reviews and either confirms or overrides.
7. The feedback loop updates model and policy behavior.

---

## Sample Data and Output Contracts

### Input Alert Contract

```json
{
  "alert_id": "string",
  "device": "string",
  "event_type": "string",
  "timestamp": "ISO-8601 datetime",
  "source_ip": "ip-string",
  "user": "string"
}
```

### Triage Output Contract

```json
{
  "risk_score": "0-100 integer",
  "confidence": "0.0-1.0 float",
  "recommended_action": "string",
  "reasoning_summary": "string"
}
```

Use strict contracts to prevent unstructured output drift and simplify downstream automation.

---

## Design Principles

- **Modular agent architecture**: triage, detection, and chat components remain independently evolvable.
- **Deterministic outputs**: JSON schemas support automation and auditability.
- **RAG over guessing**: decisions are grounded in retrievable context.
- **SOC-centric UX**: optimize for analyst speed, trust, and case quality.

---

## Future Enhancements

- Multi-agent coordination framework
- Persistent memory across incident timelines
- More formal confidence calibration models
- Threat intelligence API integrations
- Automated playbook execution hooks
- Adaptive false-positive suppression

---

## Disclaimer

This project is a simulated educational lab. It does **not** connect to production SIEM/XDR systems by default.

## Author

Brian Hannigan  
AI Security Engineer  
Detection Engineering | SOC Automation | Agentic AI Systems
