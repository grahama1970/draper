# 🧠 AI-Driven Formal Verification Planner for DO-254

> **Note:** On-premises deployment significantly aids ITAR compliance by keeping data internal. Full compliance requires additional organizational controls (access restrictions, audits, training, documentation).

---

## 📜 Abstract

Develop an MCP-compatible, on-premises agentic R&D system (**draper-mcp-formal-planner**) to accelerate and enhance DO-254 formal verification of Draper's radiation-hardened hardware. The system leverages curated historical data, integrates SEU models, automates artifact generation, assists in AI-driven CEGAR loops, and incorporates human oversight for critical decisions.

---

## 🔑 Core Components

- **🤖 Agentic Workflow (MCP):**  
  Orchestrated by `Boomerang`, coordinating:  
  `Planner`, `DraperRetriever`, `SeniorCoder-Formal`, `FormalVerifier`, `RadHardSpecialist`.  
  Includes mandatory human-in-the-loop steps for property validation and complex counterexample analysis.

- **🔍 Curated Data Retriever:**  
  `DraperRetriever` agent queries a vector database of curated historical verification assets. Requires initial data curation.

- **🛠️ EDA Tool Abstraction Layer:**  
  Python library wrapping formal tool interactions (initially VC Formal) and license management.

- **📄 Artifact Generation:**  
  `SeniorCoder-Formal` generates vPlans, SEU-aware SVAs, and covergroups aligned with DO-254 objectives.

- **🔄 AI-Assisted CEGAR:**  
  `FormalVerifier` runs proofs and annotates counterexamples.  
  `SeniorCoder-Formal` analyzes annotated CEX, suggests refinements, escalates complex cases for human review.

- **☢️ Radiation Awareness:**  
  `RadHardSpecialist` integrates Draper SEU models into property generation and analysis.

- **⚡ Resource Management:**  
  `Boomerang` manages license checks and job queuing.

- **📦 Packaging & Deployment:**  
  Secure on-premises deployment via Docker and `uv`, requiring GPU resources (H100 recommended).

---

## 🔄 Recovery Plan (If Session Crashes)

1. 🔍 Review `taskplan.md` for last completed task `[X]`.
2. ▶️ Resume at the first incomplete task `[ ]`.
3. 🚀 Relaunch environment and dependencies (`docker compose up -d`), restart MCP agents.
4. 📝 Instruct `Planner` to continue.

---

## 📅 Task Plan Visualization

*(See `taskplan.md` for detailed Gantt chart)*

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#f0f0f0'}}}%%
gantt
    dateFormat  YYYY-MM-DD
    title       Formal Planner Task Plan (v2.1 - Final)
    excludes    weekends

    section "Setup, Curation & Retrieval (8 weeks)"
    Initialize Project & Dependencies          :2025-04-22, 5d
    Historical Data Curation                   :after P1T1, 10d
    Vectorize & Index Data                     :after P1T2, 7d
    Implement DraperRetriever Agent            :after P1T3, 7d
    Implement Validation & HITL Hook           :after P1T4, 5d
    Phase 1 Verification & Demo                :after P1T5, 2d

    section "Formal Tool Abstraction & SEU Integration (8 weeks)"
    Develop EDA Abstraction Layer              :after P1T6, 10d
    Integrate License Management               :after P2T1, 5d
    Implement FormalVerifier Agent             :after P2T1, 7d
    Define SEU Model Strategy & Scope          :after P1T6, 5d
    Implement RadHardSpecialist                :after P2T4, 5d
    SEU Injection in SVA Generation            :after P2T5, 5d
    Phase 2 Verification & Demo                :after P2T6, 2d

    section "Artifact Generation & AI-Enhanced CEGAR (9 weeks)"
    SeniorCoder vPlan/SVA Generation           :after P2T7, 7d
    Covergroup Generation                      :after P3T1, 7d
    CEX Annotation in FormalVerifier           :after P2T7, 6d
    CEX Analysis & Suggestion                  :after P3T3, 7d
    Implement CEGAR Loop & HITL                :after P3T4, 7d
    Integrate DO-254 Reporting Hooks           :after P3T5, 3d
    Phase 3 Verification & Demo                :after P3T6, 2d

    section "End-to-End Testing & Documentation (5 weeks)"
    Develop E2E Test Cases                     :after P3T7, 5d
    Execute Tests & Performance Tuning         :after P4T1, 7d
    Refine Retrieval & CEX Analysis            :after P4T2, 5d
    Finalize Documentation & User Guides       :after P4T3, 3d
    Final Demo & Handover                     :after P4T4, 2d
```

---

## 🗺️ Phase Legend

| Short Title                   | Full Description                                         |
|-------------------------------|----------------------------------------------------------|
| **Setup & Retrieval**         | Setup, Curation & Retrieval (8 Weeks)                   |
| **Tool & SEU Integration**    | Formal Tool Abstraction & SEU Integration (8 Weeks)     |
| **Artifact & AI-CEGAR**       | Artifact Generation & AI-Enhanced CEGAR (9 Weeks)       |
| **Testing & Docs**            | End-to-End Testing & Documentation (5 Weeks)            |

---

## 📝 Final Notes

- ☢️ Integrates **SEU models** into property generation and verification.
- 🔄 Uses **AI-assisted CEGAR** with human-in-the-loop review for certification rigor.
- 📄 Automates artifact generation aligned with DO-254.
- 🏢 Designed for **secure, on-premises deployment** with scalable GPU acceleration.