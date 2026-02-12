# 🛡️ SOC Agent Lab
Autonomous AI Agents for SOC Alert Triage, Detection Engineering, and Log Intelligence

---

## 📌 Overview

SOC Agent Lab is a simulated AI-powered Security Operations Center (SOC) automation platform.

This project demonstrates how autonomous AI agents can:

- Automate alert triage
- Generate detection rules
- Perform contextual log search
- Reduce false positives
- Implement feedback-driven learning loops
- Scale SOC coverage without scaling headcount

It models the architecture used by modern AI-driven SOC platforms.

---

## 🎯 Project Goals

This lab simulates:

- Ingestion of structured security alerts
- Embedding-based contextual retrieval (RAG)
- Agent-based reasoning workflows
- Detection-as-Code generation
- Feedback loops for confidence calibration
- Modular AI orchestration architecture

The objective is to demonstrate real-world AI engineering patterns applied to security operations.

---

## 🏗️ Architecture Overview
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


---

## 📂 Repository Structure



soc-agent-lab/
│
├── ingestion/
│ └── alert_simulator.py
│
├── embeddings/
│ └── embed_pipeline.py
│
├── vector_store/
│ └── vectordb.py
│
├── agents/
│ ├── triage_agent.py
│ ├── detection_agent.py
│ └── chat_agent.py
│
├── evaluation/
│ └── feedback_loop.py
│
├── api/
│ └── main.py
│
├── data/
│ └── sample_alerts.json
│
├── requirements.txt
└── README.md


---

# 🔎 System Components

---

## 1️⃣ Alert Ingestion Layer

Simulates SIEM/XDR alerts.

Example alert structure:

```json
{
  "alert_id": "A-1023",
  "device": "WS-445",
  "event_type": "RemoteInteractiveLogon",
  "timestamp": "2025-11-19T02:13:22Z",
  "source_ip": "185.231.44.12",
  "user": "svc_admin"
}


This represents:

Off-hours login

Public IP

Privileged account

Potential lateral movement

2️⃣ Embedding Pipeline

Purpose:

Convert alerts and historical data into vector representations for similarity search.

Why Embeddings?

Embeddings allow the system to:

Find similar historical incidents

Identify repeat false positives

Contextualize new alerts

Perform semantic search across logs and playbooks

3️⃣ Vector Database (RAG Context Layer)

Implements Retrieval-Augmented Generation (RAG).

Workflow:

New Alert → Embed → Search Vector DB → Retrieve Similar Alerts + Playbooks


The agent then reasons with:

Current alert

Similar historical alerts

Internal SOC playbooks

Threat intelligence references

4️⃣ Triage Agent

Primary responsibilities:

Risk scoring

Confidence scoring

Escalation decision

Reasoning summary

Example output:

{
  "risk_score": 87,
  "confidence": 0.91,
  "recommended_action": "Escalate to Tier 2",
  "reasoning_summary": "Off-hours admin logon from foreign IP with no prior baseline behavior"
}


Key design principles:

Structured JSON outputs

Deterministic fields

Confidence calibration

Explainable reasoning

5️⃣ Detection Generation Agent

Generates detection rules based on alert patterns.

Example output (KQL):

DeviceLogonEvents
| where LogonType == "RemoteInteractive"
| where RemoteIPType == "Public"
| where Timestamp between (ago(7d) .. now())
| summarize count() by DeviceName


This enables:

Detection-as-Code automation

Rule templating

SOC engineering acceleration

Continuous improvement of detection coverage

6️⃣ Chat Agent

Interactive assistant for analysts.

Capabilities:

Natural language log search

Incident explanation

Detection refinement

Threat intel summarization

Example:

“Show me all off-hours interactive logons from public IP addresses in the past 7 days.”

7️⃣ Feedback Loop Engine

Critical component.

Tracks:

Analyst overrides

False positive classifications

Escalation correctness

Confidence misalignment

Implements:

Confidence recalibration

Risk weighting adjustment

Prompt optimization inputs

Continuous learning signals

🧠 Engineering Design Principles
Modular Agent Architecture

Each agent is independent:

Triage Agent

Detection Agent

Chat Agent

Risk Agent

Agents communicate via structured alert objects.

Deterministic Output Contracts

Agents return strict schemas:

No free-form hallucinated output

JSON-only responses

Explicit confidence scores

RAG over Hallucination

Agents do not guess.

They:

Retrieve context

Ground reasoning in retrieved data

Provide explainable output

SOC-Centric Thinking

The system is designed to:

Reduce analyst cognitive load

Reduce triage time

Improve escalation accuracy

Increase alert throughput

Improve signal-to-noise ratio

📈 Example End-to-End Flow

Alert is generated (off-hours admin logon).

Alert is embedded.

Vector DB returns 5 similar historical alerts.

Triage Agent evaluates severity.

Detection Agent generates a new rule.

Analyst reviews recommendation.

Analyst override is logged.

Feedback loop updates confidence model.

🧪 Simulated Use Case

Scenario:

Service account logs in at 2:13 AM

From public IP

No recent baseline history

System Response:

Risk Score: 87

Confidence: 0.91

Escalate to Tier 2

Generate detection rule

Recommend network containment check

📊 Key Concepts Demonstrated

Agentic AI system design

Embedding-based contextual reasoning

Retrieval-Augmented Generation (RAG)

Detection-as-Code automation

Feedback-driven confidence tuning

Security automation engineering

SOC workflow integration

🚀 Future Enhancements

Multi-agent coordination framework

Memory persistence layer

Confidence calibration modeling

Threat intelligence API integration

Auto-playbook execution

Adaptive false positive suppression

🧩 Why This Project Matters

Modern SOC teams face:

Alert fatigue

Staffing shortages

Increasing telemetry volume

High false positive rates

AI agents can:

Scale coverage 5–10x

Reduce triage time

Improve detection quality

Enable analysts to focus on true threats

This lab models that future.

🔐 Disclaimer

This project is a simulated educational lab.

It does not connect to real SIEM, XDR, or production environments.

👤 Author

Brian Hannigan
AI Security Engineer
Detection Engineering | SOC Automation | Agentic AI Systems
