# Objective: AI-Driven Formal Verification Planner for DO-254

Develop an MCP-compatible, on-premises agentic system (**draper-mcp-formal-planner**) as an R&D project. This system will act as an AI assistant to Draper's verification engineers, aiming to accelerate and enhance the rigor of the DO-254 formal verification process for radiation-hardened systems. It leverages curated historical verification data, integrates SEU models, automates artifact generation (SVA, covergroups), assists in counterexample analysis (CEGAR), and incorporates human oversight for key decisions.

# Core Components:

*   **Agentic Workflow (MCP):** Orchestrated by `Boomerang`, coordinating specialized agents (`Planner`, `DraperRetriever`, `SeniorCoder-Formal`, `FormalVerifier`, `RadHardSpecialist`). Includes mandatory Human-in-the-Loop steps for property validation and complex CEX analysis.
*   **Curated Data Retriever:** `DraperRetriever` agent querying a vector database populated with pre-curated historical verification assets (properties, reports, waivers). Requires initial data curation effort.
*   **EDA Tool Abstraction Layer:** Python library wrapping interactions with formal verification tools (initially VC Formal) and potentially license management APIs. Enables tool flexibility and isolates agents from direct tool commands.
*   **Artifact Generation:** `SeniorCoder-Formal` agent generating verification plans (vPlans), SystemVerilog Assertions (SVA) incorporating SEU model guidance, and SystemVerilog covergroups based on DO-254 objectives and design analysis.
*   **AI-Assisted CEGAR:** `FormalVerifier` runs proofs and annotates Counterexamples (CEX) with key signal values. `SeniorCoder-Formal` analyzes annotated CEX to suggest property refinements or bug locations, escalating complex cases for human review.
*   **Radiation Awareness:** Integration of Draper SEU models (via `RadHardSpecialist` guidance) into SVA generation and formal analysis, focusing on verifying behavior under potential SEU conditions. Scope limited to functional correctness/mitigation checks.
*   **Resource Management:** `Boomerang` utilizes the EDA Abstraction Layer to check license availability (best-effort based on available API) and manage job queuing.
*   **Packaging & Deployment:** Designed for secure on-premises deployment using Docker containers, managed via `docker-compose.yml`, Python packaging via `pyproject.toml` / `uv`. Requires GPU resources (H100 recommended).

# Recovery Plan (If Session Crashes):

1.  **Identify Last Completed Task:** Review this task plan document (`taskplan.md`) for the last task marked `[X]`.
2.  **Identify Next Task:** Resume development at the first task marked `[ ]`.
3.  **Restart Process:** Relaunch the development environment. Ensure Docker containers (`docker compose up -d`) for dependencies like the Vector DB are running if needed. Relaunch the MCP agent framework.
4.  **Continue Development:** Instruct the `Planner` agent to proceed with the next identified task.

---

# Task Plan Visualization

```mermaid
gantt
    dateFormat  YYYY-MM-DD
    title       Formal Planner Detailed Task Plan (v2.1 - Final)
    excludes    weekends

    section Phase 1: Setup, Curation & Retrieval (8 Weeks)
    Initialize Project Structure & Base Dependencies : P1T1, 2025-04-22, 5d
    Establish Historical Data Curation Process     : P1T2, after P1T1, 10d
    Vectorize & Index Curated Draper Data          : P1T3, after P1T2, 7d
    Implement DraperRetriever MCP Agent (Curated)  : P1T4, after P1T3, 7d
    Implement Retriever Validation & HITL Hook     : P1T5, after P1T4, 5d
    Phase 1 Verification, Demo & Finalization      : P1T6, after P1T5, 2d

    section Phase 2: Formal Tool Abstraction & SEU Integration (8 Weeks)
    Develop EDA Tool Abstraction Layer (VC Formal) : P2T1, after P1T6, 10d
    Integrate License Management Check (Best Effort): P2T2, after P2T1, 5d
    Implement FormalVerifier Agent (uses EDALayer) : P2T3, after P2T1, 7d
    Define SEU Model Ingestion Strategy & Scope    : P2T4, after P1T6, 5d
    Implement RadHardSpecialist Logic              : P2T5, after P2T4, 5d
    Implement SEU Injection in SVA Gen (Coder)     : P2T6, after P2T5, 5d
    Phase 2 Verification, Demo & Finalization      : P2T7, after P2T6, 2d

    section Phase 3: Artifact Generation & Enhanced CEGAR (9 Weeks)
    Implement SeniorCoder vPlan/SVA Generation     : P3T1, after P2T7, 7d
    Implement Covergroup Generation (Coder)        : P3T2, after P3T1, 7d
    Implement CEX Annotation (Verifier/EDALayer)   : P3T3, after P2T7, 6d
    Implement CEX Analysis & Suggestion (Coder)    : P3T4, after P3t3, 7d
    Implement CEGAR Loop & HITL Hook (Boomerang)   : P3T5, after P3t4, 7d
    Integrate DO-254 Report Generation Hooks       : P3T6, after P3t5, 3d
    Phase 3 Verification, Demo & Finalization      : P3T7, after P3t6, 2d

    section Phase 4: End-to-End Testing, Tuning & Documentation (5 Weeks)
    Develop E2E Test Cases (Multiple Designs)      : P4T1, after P3T7, 5d
    Execute E2E Tests & Performance Tuning         : P4T2, after P4T1, 7d
    Refine Retrieval Accuracy & CEX Analysis Help  : P4T3, after P4t2, 5d
    Finalize Documentation & User Guides (HITL Proc): P4T4, after P4t3, 3d
    Final Demo & Handover                          : P4T5, after P4t4, 2d
Use code with caution.
Markdown
Final Task Plan: draper-mcp-formal-planner Development
Phase 1: Setup, Curation & Retrieval

Task 1.1: Initialize Project Structure & Base Dependencies

Action: Create directory structure (src/draper_formal_planner, scripts, repo_docs, src/draper_formal_planner/docs). Initialize pyproject.toml (using uv), .gitignore. Add core dependencies (fastapi, vector DB client e.g., pymilvus, mcp-core, langchain/llama-index potentially for retrieval). Setup secure environment prerequisites (Docker, CUDA). Setup Vector DB instance. Create placeholder lessons_learned.json.

Deliverable: Project directory structure, pyproject.toml, uv.lock, .gitignore, lessons_learned.json, base environment functional, Vector DB running.

Task 1.2: Establish Historical Data Curation Process

Action: Requires significant Draper input. Define the process for identifying, extracting, cleaning, and structuring historical verification data (properties, reports, associated metadata like project, block, status, DO-254 objective). Develop scripts/guidelines for this curation. Perform initial curation on a representative sample dataset provided by Draper.

Deliverable: Data Curation Process Document, curation scripts (scripts/curate_history_data.py), initial curated dataset (e.g., JSONL files with text and metadata).

Task 1.3: Vectorize & Index Curated Draper Data

Action: Develop scripts (scripts/index_curated_data.py) to read the curated data, generate text embeddings (using a suitable sentence transformer model), and ingest the data (text, embeddings, metadata) into the configured Vector DB.

Deliverable: Indexing scripts, Vector DB populated with initial curated and vectorized data.

Task 1.4: Implement DraperRetriever MCP Agent (Curated)

Action: Create the .roomodes definition for DraperRetriever. Implement the agent's Python code. Logic should accept context queries from Boomerang, perform hybrid searches (vector + metadata filtering) against the curated Vector DB, rank results, and return a structured list of relevant historical assets.

Deliverable: draper_retriever.py agent code, draper-retriever roomode definition.

Task 1.5: Implement Retriever Validation & HITL Hook

Action: Implement logic in SeniorCoder-Formal to perform a quick validation check on properties returned by DraperRetriever. Implement logic in Boomerang to check the validation flags and, if necessary, trigger the ask_human prompt via Planner for review of retrieved properties.

Deliverable: Updated SeniorCoder-Formal with validation function. Updated Boomerang with HITL hook for retrieval validation.

Task 1.6: Phase 1 Verification, Demo & Finalization

Goal: Verify project setup, data curation feasibility, indexing, retrieval functionality, and the HITL hook for retrieval validation.

Actions: Review code/scripts/docs, demonstrate the indexing process, run test queries against the Vector DB via the DraperRetriever agent, show the validation check and HITL trigger, git add ., git commit -m "Complete Phase 1: Setup, Curation & Retrieval", git tag v0.1-retrieval, update lessons learned (especially on curation).

Deliverable:

Project structure and dependencies initialized.

Data curation process defined, initial dataset curated and indexed.

DraperRetriever agent implemented and functional.

Retrieval validation and HITL hook implemented.

Demo showcasing data indexing, retrieval, and validation workflow.

Phase 2: Formal Tool Abstraction & SEU Integration

Task 2.1: Develop EDA Tool Abstraction Layer (VC Formal initially)

Action: Create/Expand the Python module (src/draper_formal_planner/eda_abstraction.py). Define classes/functions to wrap interactions specifically for formal tools (initially VC Formal). Include methods for preparing run scripts, launching jobs, parsing standard output/log files, extracting pass/fail status, coverage data, and CEX traces.

Deliverable: eda_abstraction.py module with robust wrappers for VC Formal operations.

Task 2.2: Integrate License Management Check (Best Effort)

Action: Implement license checking logic within eda_abstraction.py for VC Formal, based on Draper feedback on available methods (API call, lmstat wrapper). Handle cases where checking might not be possible gracefully (e.g., log warning, proceed). Update Boomerang to call this check.

Deliverable: Updated eda_abstraction.py with license check function. Updated Boomerang agent integrating the check.

Task 2.3: Implement FormalVerifier Agent (uses EDALayer)

Action: Create the .roomodes definition for FormalVerifier. Implement the agent's Python code. Logic should accept verification tasks from Boomerang and use the eda_abstraction.py module exclusively to prepare, execute, and parse results from the formal tool.

Deliverable: formal_verifier.py agent code, formal-verifier roomode definition.

Task 2.4: Define SEU Model Ingestion Strategy & Scope

Action: Based on Draper feedback, analyze SEU model format. Design the strategy for ingesting this data. Crucially, define and document the scope of SEU modeling within formal (e.g., focus on state recovery properties, disable iff clauses, not full transient simulation).

Deliverable: Design document for SEU model ingestion and formal scope definition. Code stubs/interfaces for SEU data access.

Task 2.5: Implement RadHardSpecialist Logic

Action: Create the .roomodes definition for RadHardSpecialist. Implement the agent's Python code. Logic should access SEU data (per strategy in 2.4) and provide SVA templates or guidance for incorporating SEU considerations into properties, reporting back to Boomerang.

Deliverable: rad_hard_specialist.py agent code, rad-hard-specialist roomode definition.

Task 2.6: Implement SEU Injection in SVA Gen (Coder)

Action: Modify the SeniorCoder-Formal agent. When generating new SVA properties, incorporate guidance received from RadHardSpecialist (via Boomerang) to make properties rad-aware within the defined scope (e.g., add appropriate disable iff conditions).

Deliverable: Updated SeniorCoder-Formal agent capable of generating rad-aware SVA.

Task 2.7: Phase 2 Verification, Demo & Finalization

Goal: Verify the EDA abstraction layer for formal tools, license checking, SEU context integration, and rad-aware SVA generation.

Actions: Review code, demonstrate running a formal job via the FormalVerifier using the abstraction layer, show license check interaction (or logging), demonstrate SeniorCoder-Formal generating SVA with SEU considerations based on RadHardSpecialist input, git add ., git commit -m "Complete Phase 2: Abstraction & SEU Integration", git tag v0.2-abstraction, update lessons learned.

Deliverable:

EDA Abstraction layer implemented for VC Formal.

License checking integrated (best-effort).

FormalVerifier agent implemented using abstraction.

SEU model ingestion strategy and scope defined.

RadHardSpecialist implemented.

SeniorCoder-Formal generating rad-aware SVA.

Demo showcasing abstracted formal run and SEU-aware SVA generation.

Phase 3: Artifact Generation & Enhanced CEGAR

Task 3.1: Implement SeniorCoder vPlan/SVA Generation

Action: Enhance SeniorCoder-Formal to fully generate comprehensive vPlans (linking properties to requirements/design blocks) and complete SVA files based on design analysis, approved retrieved properties, newly authored rad-aware properties, and DO-254 objectives.

Deliverable: Updated SeniorCoder-Formal agent producing well-structured vPlan documents and SVA files.

Task 3.2: Implement Covergroup Generation (Coder)

Action: Further enhance SeniorCoder-Formal to automatically generate SystemVerilog covergroups based on the properties defined in the vPlan and standard DO-254 coverage models (e.g., condition coverage, toggle coverage on key signals related to assertions).

Deliverable: Updated SeniorCoder-Formal agent generating functional SV covergroup code.

Task 3.3: Implement CEX Annotation (Verifier/EDALayer)

Action: Implement the CEX annotation logic within the FormalVerifier agent, likely using helper functions within the eda_abstraction.py module. This involves parsing the raw CEX trace from the formal tool and extracting/formatting key signal values into a separate, easily digestible report file.

Deliverable: Updated FormalVerifier and eda_abstraction.py capable of generating annotated CEX reports.

Task 3.4: Implement CEX Analysis & Suggestion (Coder)

Action: Implement the CEX analysis capability in SeniorCoder-Formal. The agent should read the annotated CEX report, attempt to correlate failing signals/times with the SVA property and design code, and generate a structured suggestion (refined property or bug location) along with a confidence score.

Deliverable: Updated SeniorCoder-Formal agent performing AI-assisted CEX analysis and suggestion.

Task 3.5: Implement CEGAR Loop & HITL Hook (Boomerang)

Action: Implement the full CEGAR loop logic in Boomerang. If FormalVerifier reports a CEX, orchestrate the flow: task SeniorCoder-Formal for analysis, check analysis confidence, escalate to Planner/Human if confidence is low or CEX deemed complex, receive confirmed refinement, task SeniorCoder-Formal to update SVA, task FormalVerifier to re-run the specific proof.

Deliverable: Updated Boomerang agent orchestrating the complete CEGAR loop with HITL for complex analysis.

Task 3.6: Integrate DO-254 Report Generation Hooks

Action: Modify agents (Boomerang, FormalVerifier, Coder) to log key events and results (property status, coverage metrics, CEX analysis outcomes, HITL decisions) with metadata suitable for later aggregation into DO-254 compliance reports. Define structure for traceability artifacts.

Deliverable: Enhanced logging/reporting mechanisms supporting DO-254 artifact generation.

Task 3.7: Phase 3 Verification, Demo & Finalization

Goal: Verify the generation of vPlan/SVA/Covergroups and the functioning of the AI-assisted CEGAR loop with CEX annotation and HITL.

Actions: Review code, demonstrate generation of artifacts for a test case, run a test case that produces a CEX, show CEX annotation, demonstrate SeniorCoder-Formal analysis/suggestion, show HITL escalation for a complex case, demonstrate loop re-running proof after refinement, git add ., git commit -m "Complete Phase 3: Artifact Gen & CEGAR", git tag v0.3-cegar, update lessons learned.

Deliverable:

SeniorCoder-Formal generating vPlan, SVA, Covergroups.

CEX annotation implemented in FormalVerifier.

AI-assisted CEX analysis implemented in SeniorCoder-Formal.

CEGAR loop with HITL implemented in Boomerang.

DO-254 reporting hooks added.

Demo showcasing artifact generation and the full CEGAR loop.

Phase 4: End-to-End Testing, Tuning & Documentation

Task 4.1: Develop E2E Test Cases (Multiple Designs)

Action: Create end-to-end test scripts and scenarios (scripts/test_formal_e2e.py) using representative Draper design examples (if possible) or realistic benchmark designs. Test cases should cover retrieval, generation, successful verification, and multiple iterations of the CEGAR loop (including HITL prompts).

Deliverable: E2E test suite document and associated scripts/data.

Task 4.2: Execute E2E Tests & Performance Tuning

Action: Run the E2E test suite, measure performance (e.g., time per phase, HITL interaction time), identify bottlenecks (e.g., retrieval speed, CEX analysis time, formal tool runtime), and tune agent logic, prompts, or resource allocation where possible.

Deliverable: Documented E2E test results, performance analysis report, code/configuration adjustments based on tuning.

Task 4.3: Refine Retrieval Accuracy & CEX Analysis Helpfulness

Action: Based on E2E testing, specifically evaluate the quality of retrieved properties and the usefulness of the CEX analysis suggestions. Refine retrieval prompts, metadata filters, embedding models, or CEX analysis logic as needed. May require re-curation/re-indexing if retrieval is poor.

Deliverable: Report on retrieval/CEX analysis quality, implemented refinements.

Task 4.4: Finalize Documentation & User Guides (HITL Proc)

Action: Update the main README.md. Create user guides detailing system setup, configuration, execution, and specifically the procedures for verification engineers interacting with HITL prompts (retrieval validation, CEX analysis review).

Deliverable: Finalized README.md, User Guide document(s).

Task 4.5: Final Demo & Handover

Goal: Demonstrate the fully integrated system operating on a representative E2E scenario, showcasing R&D achievements, AI assistance features, and HITL workflow. Handover documentation and codebase.

Actions: Prepare final demo scenario, present E2E results and tuning insights, walk through documentation and HITL procedures, perform final code commit (git add ., git commit -m "Complete Phase 4: Final Testing & Documentation", git tag v1.0-final), archive project state.

Deliverable: Successful final demonstration, completed documentation, final tagged codebase.

