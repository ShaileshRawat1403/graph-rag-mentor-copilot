---
title: "MentorBridge"
archetype: "onboarding_guide"
status: "Internal (Hackathon / Learning Prototype)"
owner: "Team MentorBridge"
maintainer: "Team MentorBridge"
version: "0.2"
last_reviewed: "2026-01-28"
tags: ["azure", "rag", "knowledge-graph", "mentoring", "onboarding"]
---


# MentorBridge (Graph‑RAG Mentor Copilot)

This is a **hands‑on exploration project**, inspired by a hackathon use‑case.
It is **not** an official submission and **not** a production product.

The goal is learning:

* how Azure AI services fit together
* how RAG + lightweight Knowledge Graphs improve guidance
* how AI can reduce *operational friction* without removing humans

The demo context represents a **generic youth mentorship and skilling organization**.

---

## Why This Project Exists

This project explores two real problems:

1. **Mentor decision fatigue**
2. **Slow, manual onboarding workflows**

Instead of solving them with a single chatbot, we split responsibilities clearly.

---

## What This App Does (At a Glance)

* Dual‑purpose web app:

  * **Mentor Copilot** (guidance + reasoning)
  * **Onboarding Velocity Engine** (document verification)

* Mentor dashboard with:

  * student selector
  * session persistence (SQLite)

* AI‑assisted reasoning using:

  * RAG over `students.csv`
  * JSON Knowledge Graph rules

* Automated document verification using:

  * Azure AI Document Intelligence
  * human‑in‑the‑loop escalation on low confidence

---

## Tech Stack (Why Each Piece Exists)

### Backend

* **Spring Boot** – orchestration + business logic
* **Spring AI** – structured LLM access
* **Azure OpenAI** – reasoning + explanation
* **PGVector** – similarity search (RAG)

### Frontend

* **Streamlit** – fast, explainable UI for demos

### Storage

* **SQLite** – mentor chat sessions
* **PostgreSQL** – student profiles + vectors

---

## Quickstart (Run Locally)

### 1) Backend

```bash
mvn spring-boot:run
```

If this fails, nothing else will work.

---

### 2) Frontend

```bash
streamlit run app.py
```

Open the URL shown in the terminal.

---

### 3) UI Dependencies

```bash
pip install plotly streamlit-agraph
```

---

## Required Environment Variables

```bash
export AZURE_OPENAI_ENDPOINT="https://<your-resource>.openai.azure.com/"
export AZURE_OPENAI_API_KEY="<your-key>"
export AZURE_OPENAI_CHAT_DEPLOYMENT="gpt-4o"
export AZURE_OPENAI_EMBEDDING_DEPLOYMENT="text-embedding-ada-002"
```

Only Azure OpenAI is required to demo the Mentor Copilot.

---

## Project Structure (Mental Map)

* `app.py`

  * Streamlit mentor dashboard + onboarding UI

* `src/main/java/...`

  * Spring Boot APIs

* `src/main/resources/students.csv`

  * synthetic student profiles

* `src/main/resources/knowledge_graph.json`

  * interest → trait → skill → role rules

* `mentor_sessions.db`

  * mentor chat history (SQLite)

---

## Mentor Copilot – Process Flow

```mermaid
flowchart TD
    A[Student Profile + Surveys] --> B[RAG: Similar Student Retrieval]
    A --> C[Knowledge Graph Rules]
    B --> D[Mentor Copilot Prompt]
    C --> D
    D --> E[Roadmap + Questions + Resources]
    E --> F[Mentor Review]
    F --> G[Student Guidance + Progress Tracking]
    G --> A
```

**Key idea:**
AI assists mentors, mentors remain accountable.

---

## Mentor Copilot – Architecture

```mermaid
flowchart LR
    subgraph UI[Streamlit Mentor Dashboard]
        U1[Student Selector]
        U2[Mentor Copilot Chat]
        U3[Skill Bridge Radar]
        U4[Knowledge Graph Explorer]
    end

    subgraph API[Spring Boot API]
        C1[CareerAdvisorController]
        S1[CareerAdvisorService]
        T1[Curriculum Tool]
        DL[DataLoadingService]
    end

    subgraph Data[Data & Storage]
        CSV[students.csv]
        KG[knowledge_graph.json]
        VS[PGVector]
        DB[mentor_sessions.db]
    end

    subgraph AI[Azure OpenAI]
        M1[Chat Model]
        E1[Embedding Model]
    end

    U1 --> C1
    U2 --> C1
    U3 --> C1
    U4 --> C1

    C1 --> S1
    S1 --> T1
    S1 --> M1
    S1 --> E1
    DL --> VS

    CSV --> DL
    KG --> S1
    DB --> U2
    VS --> S1
```

---

## Onboarding Velocity Engine

This module accelerates learner onboarding while keeping humans in control.

### What It Does

* Captures Aadhaar, PAN, and income documents
* Extracts fields using **Azure AI Document Intelligence**
* Applies confidence‑based verification rules
* Escalates uncertain cases for human review

---

### Required Environment Variables

```bash
export AZURE_DOCINTEL_ENDPOINT="https://<your-resource>.cognitiveservices.azure.com"
export AZURE_DOCINTEL_KEY="<your-key>"
export POWER_AUTOMATE_WEBHOOK_URL="<your-flow-http-trigger-url>"
```

---

### Document Verification Logic (Current)

1. Azure Document Intelligence extracts:

   * FullName
   * DateOfBirth
   * DocumentNumber
   * Income (if present)

2. Java validation checks:

   * Normalized document name must match `student_profiles.full_name`
   * Confidence score must be **≥ 0.90**

3. If any confidence < 0.90:

   * A webhook is sent for human review

---

## Dual‑Purpose Architecture (Mentor + Onboarding)

```mermaid
flowchart LR
    subgraph UI[Streamlit Web App]
        U1[Mentor Copilot]
        U2[Onboarding Velocity Engine]
    end

    subgraph API[Spring Boot Services]
        A1[Advisor API]
        A2[Onboarding API]
    end

    subgraph AI[Azure AI]
        C1[Azure OpenAI]
        C2[Document Intelligence]
    end

    subgraph Data[Data Stores]
        S1[PGVector]
        S2[PostgreSQL]
        S3[SQLite Sessions]
        KG[Knowledge Graph JSON]
    end

    U1 --> A1 --> C1
    A1 --> S1
    A1 --> KG

    U2 --> A2 --> C2
    A2 --> S2
    A2 -->|Low confidence| P[Power Automate Alert]

    U1 --> S3
    U2 --> S3
```

---

## Important Notes

* This is a **learning prototype**
* No model fine‑tuning is performed
* All logic is explainable and inspectable

If you can explain:

* what problem is solved
* why AI is used
* where humans stay in control

Then the system is doing its job.
