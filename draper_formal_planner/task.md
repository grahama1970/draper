# 🎯 Objective: AI-Driven Formal Verification Planner for DO-254

Develop an MCP-compatible, on-premises agentic R&D system (**draper-mcp-formal-planner**) to accelerate and enhance DO-254 formal verification of Draper's radiation-hardened hardware. The system leverages curated historical data, integrates SEU models, automates artifact generation, assists in AI-driven CEGAR loops, and incorporates human oversight for critical decisions.

---

## 🏗️ Core Components

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

    section Phase 1: Setup, Curation & Retrieval (8 Weeks)
    Initialize Project & Dependencies          :done, P1T1, 2025-04-22, 5d
    Historical Data Curation                   :active, P1T2, after P1T1, 10d
    Vectorize & Index Data                     :P1T3, after P1T2, 7d
    Implement DraperRetriever Agent            :P1T4, after P1T3, 7d
    Implement Validation & HITL Hook           :P1T5, after P1T4, 5d
    Phase 1 Verification & Demo                :P1T6, after P1T5, 2d

    section Phase 2: Formal Tool Abstraction & SEU Integration (8 Weeks)
    Develop EDA Abstraction Layer              :P2T1, after P1T6, 10d
    Integrate License Management               :P2T2, after P2T1, 5d
    Implement FormalVerifier Agent             :P2T3, after P2T1, 7d
    Define SEU Model Strategy & Scope          :P2T4, after P1T6, 5d
    Implement RadHardSpecialist                :P2T5, after P2T4, 5d
    SEU Injection in SVA Generation            :P2T6, after P2T5, 5d
    Phase 2 Verification & Demo                :P2T7, after P2T6, 2d

    section Phase 3: Artifact Generation & AI-Enhanced CEGAR (9 Weeks)
    SeniorCoder vPlan/SVA Generation           :P3T1, after P2T7, 7d
    Covergroup Generation                      :P3T2, after P3T1, 7d
    CEX Annotation in FormalVerifier           :P3T3, after P2T7, 6d
    CEX Analysis & Suggestion                  :P3T4, after P3T3, 7d
    Implement CEGAR Loop & HITL                :P3T5, after P3T4, 7d
    Integrate DO-254 Reporting Hooks           :P3T6, after P3T5, 3d
    Phase 3 Verification & Demo                :P3T7, after P3T6, 2d

    section Phase 4: End-to-End Testing & Documentation (5 Weeks)
    Develop E2E Test Cases                     :P4T1, after P3T7, 5d
    Execute Tests & Performance Tuning         :P4T2, after P4T1, 7d
    Refine Retrieval & CEX Analysis            :P4T3, after P4T2, 5d
    Finalize Documentation & User Guides       :P4T4, after P4T3, 3d
    Final Demo & Handover                     :P4T5, after P4T4, 2d
```

---

## 📝 Final Notes

- ☢️ Integrates **SEU models** into property generation and verification.
- 🔄 Uses **AI-assisted CEGAR** with human-in-the-loop review for certification rigor.
- 🤖 Automates artifact generation aligned with DO-254.
- 🏢 Designed for **secure, on-premises deployment** with scalable GPU acceleration.
