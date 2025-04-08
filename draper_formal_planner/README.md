# AI-Driven Formal Verification Planner for DO-254 📜🔍 (v2.1 - Final)

## Abstract
An MCP-integrated, on-premises agentic system **proposed as a Research & Development (R&D) initiative** designed to significantly accelerate and improve the rigor of the DO-254 formal verification process for Draper's radiation-hardened systems. This system acts as a powerful assistant to verification engineers, automating laborious tasks while ensuring human oversight for critical decisions and analysis. It leverages Draper's historical verification data via a curated `MCPDocumentRetriever`, integrates radiation fault models (Assumption: accessible format) into the verification flow using an **EDA Tool Abstraction Layer**, intelligently generates SystemVerilog Assertions (SVA) and covergroups, utilizes **AI-assisted Counterexample-Guided Refinement (CEGAR)** with **annotated CEX analysis**, and integrates with EDA license management (Assumption: queryable system exists). The goal is to reduce manual effort, improve coverage closure rates, enhance certification artifact quality, and ensure verification adequately addresses radiation effects, all within a human-supervised workflow. Aligns with Draper's established V&V capabilities and *"Verification and Validation Capabilities"* roadmap [Ref: Draper Internal Memo 2025-Q2].

**Key Features**
- **Curated Cross-Project Assertion Reuse**: Employs a specialized `DraperRetriever` querying a *curated* vector database of historical properties, reports, and waivers. **Requires an upfront data curation phase** but yields higher relevance. Human validation of retrieved properties integrated into the workflow.
- **Abstracted & Rad-Aware Formal Verification**: Integrates with formal tools (initially Synopsys VC Formal) through an **EDA Tool Abstraction Layer**, enabling easier tool maintenance/swapping. Seamlessly incorporates Draper's SEU models into SVA generation and formal analysis, focusing on verifying functional correctness *under* potential SEU conditions (e.g., state recovery, mitigation logic). **Scope Note:** Primarily targets verifying state recovery/mitigation logic, not exhaustive transient fault analysis within the formal tool itself.
- **Intelligent Artifact Generation**: `SeniorCoder` agent generates vPlans, SVA (based on design analysis, retrieved props, DO-254 objectives), and SystemVerilog covergroups, automating significant documentation and setup tasks.
- **AI-Enhanced CEGAR with Annotated CEX**: Implements a robust CEGAR loop. The `FormalVerifier` **annotates counterexamples** (CEX) with key signal values and timing. `SeniorCoder` analyzes the annotated CEX to *suggest* property refinements or pinpoint potential design bugs, significantly aiding the engineer in debugging. **Complex CEX analysis requires human review/confirmation.**
- **Integrated Resource Management**: The `Boomerang` orchestrator interacts with Draper's EDA license management system (Assumption: queryable) via the abstraction layer to ensure efficient utilization and queue jobs appropriately.
- **Human-in-the-Loop by Design**: Ensures human control over critical steps (property approval, complex CEX interpretation, final sign-off).

## Workflow & Architecture
```mermaid
sequenceDiagram
    participant Planner as 📝 Planner (DO-254 Task)
    participant Boomerang as 🪃 Boomerang (Orchestrator)
    participant Retriever as 🔍 DraperRetriever (MCP - Curated DB)
    participant Coder as 👩‍💻 SeniorCoder (vPlan/SVA/CEX Analysis)
    participant Verifier as ⚙️ FormalVerifier (Abstracted + Annotated CEX)
    participant RadSpec as ✨ RadHardSpecialist (SEU Expertise)
    participant Human as 🧑‍🔬 Human Verifier (HITL)
    participant HistoryDB as HIST DB (Curated Vector DB)
    participant EDALayer as Abstraction Layer
    participant FormalTool as VC Formal API (or other)
    participant LicenseMgr as License Manager API (If Exists)

    Planner->>Boomerang: Assign DO-254 Verification Task (Design X, Level A)

    %% Data Retrieval & Context Setup
    Boomerang->>Retriever: Query Curated HistoryDB for Design X Context
    Retriever-->>HistoryDB: Vector + Metadata Search
    HistoryDB-->>Retriever: Ranked Historical Properties/Reports
    Retriever->>Boomerang: Curated Candidate Properties
    Boomerang->>Coder: Validate Retrieved Properties (Quick Check)
    Coder->>Boomerang: Validated/Flagged Properties
    alt Ambiguous/Low Confidence Retrieval
        Boomerang->>Human: (ask_human) Review Retrieved Properties?
        Human-->>Boomerang: Approved/Rejected Properties
    end
    Boomerang->>RadSpec: Request Relevant SEU Models/Guidance
    RadSpec-->>Boomerang: SEU Models, SVA Injection Advice

    %% vPlan & Artifact Generation
    Boomerang->>Coder: Generate vPlan: Use Validated Props, Add New SVA (Rad-aware), Gen Covergroups
    Coder->>Boomerang: Draft vPlan, SVA file, Covergroup file

    %% Verification Execution & CEGAR Loop
    loop Verification Cycle
        Boomerang->>EDALayer: Check License Availability (Formal Tool)
        EDALayer-->>LicenseMgr: Query License (If API Exists)
        LicenseMgr-->>EDALayer: Status (Available/Unavailable)
        alt License Unavailable or No API
            Boomerang->>Boomerang: Queue Job / Log Warning, Wait...
        end
        Boomerang->>Verifier: Execute Formal Verification via EDALayer (vPlan artifacts, Design X, SEU context)
        Verifier-->>EDALayer: Run Formal Tool Job
        EDALayer-->>FormalTool: Execute Tool Commands
        FormalTool-->>EDALayer: Raw Results (Pass/Fail/CEX, Coverage)
        EDALayer-->>Verifier: Parsed & Standardized Results
        Verifier->>Verifier: **Annotate CEX** (Signal Values, Timing)
        Verifier->>Boomerang: Report Verification Results (incl. Annotated CEX path)

        alt CEX Found
            Boomerang->>Coder: Analyze Annotated CEX, Suggest Refinement/Bug
            Coder->>Boomerang: Analysis Report (Suggested SVA Fix / Bug Location)
            alt Complex CEX / Low Confidence Analysis
                 Boomerang->>Human: (ask_human) Review CEX Analysis & Suggestion?
                 Human-->>Boomerang: Confirmation / Alternative Refinement / Bug Ack
            end
            Boomerang->>Coder: Update SVA based on Confirmed Refinement
            Coder->>Boomerang: Updated SVA File
            Boomerang->>Boomerang: Continue Loop (Re-run specific proof)
        else Pass / Coverage Goal Met
            Boomerang->>Planner: Final Verification Report (Proofs, Coverage, DO-254 Artifacts)
            break Loop
        end
    end
Use code with caution.
Markdown
Workflow Explanation:

Tasking & Retrieval: Planner assigns task. Boomerang queries DraperRetriever against the curated historical DB. Coder performs initial validation; Boomerang escalates to Human if retrieval is ambiguous. RadSpec provides SEU guidance.

Generation: Boomerang tasks Coder to generate the vPlan, rad-aware SVA, and covergroups using validated/approved properties and SEU guidance.

Verification Loop:

Boomerang checks license availability via EDALayer. Jobs are queued if necessary.

Boomerang tasks Verifier to run formal verification using the EDALayer.

Verifier receives results, annotates any CEX, and reports back.

CEGAR Loop & HITL:

If CEX occurs, Boomerang tasks Coder to analyze the annotated CEX and suggest fixes.

Boomerang escalates complex CEX analysis or low-confidence suggestions to the Human for confirmation/guidance.

Based on confirmed refinement, Coder updates SVA, and the specific proof is re-run.

Completion: Loop continues until properties pass and coverage goals (checked by Boomerang/Verifier) are met. Boomerang generates final report for Planner.

Implementation Strategy (Detailed Task Plan)
(See taskplan.md file for the detailed Gantt chart)

Feasibility Report
Aspect	Rating	Notes
Technical Viability	8/10	Feasible with robust engineering. EDA abstraction improves maintainability. AI-assisted CEGAR realistic. Success critically depends on data curation effort and quality.
ITAR Compliance	10/10	Designed for on-prem deployment. See dedicated "Deployment" section.
Performance	Moderate	Focus is on engineer efficiency gains through automation and AI assistance, not solely raw proof speed. HITL steps influence overall cycle time.
Certification Impact	~50-70% effort reduction	Potential savings estimate from automation & assisted CEGAR. Actual impact depends on integration quality and current baseline. Generates traceable artifacts supporting DO-254.
R&D Focus	High	This is an R&D project exploring AI to streamline DO-254 formal V&V. Success defined by demonstrating workflow, quality of generated artifacts, and helpfulness of AI assistance (CEGAR).
Data Dependency	Very High	Requires significant upfront effort in historical data curation. Retrieval and AI assistance quality directly tied to curated data quality. See Questions for Draper.
Integration Complexity	High	Requires strong integration with Draper DBs (curation needed), EDA tools (via abstraction), and potentially license management.
Deployment Strategy
Primary Recommendation: On-Premises Deployment

Rationale: Maximum security for ITAR/sensitive IP, simplified compliance, easier integration with existing on-prem EDA ecosystem.

Implementation: MCP agents, Vector DB, H100s deployed within Draper's secure facilities.

Secondary Option: Google Cloud ITAR Environment

Possibility: Use GCP Assured Workloads.

Considerations: Increased complexity (compliance, data transfer, integration), cost. Recommended only if aligned with existing Draper cloud strategy and after due diligence.

Hardware Requirements & Phased Investment
R&D Nature: Project explores feasibility; investment scales with value.

Initial Recommendation (MVP/Phase 1-2): Start with 2x NVIDIA H100 GPUs. Justification: Minimum viable platform for core LLM agents (Coder, Retriever), AI-assisted CEX analysis, and initial parallel proof experiments via MIG. Keeps initial R&D cost contained.

Phased Scaling Path: Add GPUs (potentially 2-4 total) in later phases based on validated performance needs for more complex CEX analysis, faster property generation, or increased parallel proof execution.

Key Assumptions & Questions for Draper
This proposal relies on the following assumptions and requires clarification from Draper:

Historical Verification Data:

Assumption: Draper possesses structured historical verification data (properties, reports, waivers) amenable to curation and indexing in a vector database, leveraging their focus on Digital Engineering.

Question: What is the format, location, and accessibility of this data? Crucially, what level of effort is anticipated for the initial data curation phase required to make this data usable for high-quality retrieval? Is an API available, or primarily document/database access?

SEU Model Accessibility:

Assumption: Draper's proprietary SEU models can be programmatically accessed and their effects translated into formal property constraints (e.g., conditional logic, fault injection directives).

Question: What is the format of these SEU models? How can the agent system ingest this information (API, file, DB query) to inform SVA generation?

EDA Tooling & Interfaces:

Assumption: Draper uses VC Formal and other standard EDA tools with scriptable interfaces.

Question: What is the primary formal verification tool(s) targeted for initial integration via the abstraction layer? Are there other key simulation or static analysis tools relevant to the DO-254 flow whose results could inform the planner? Are robust scripting APIs available?

License Management:

Assumption: An EDA license management system is in place.

Question: Is there an existing API or reliable method for programmatically querying EDA license availability for tools like VC Formal? (The system can function without this by simply attempting runs, but querying enables better job management).

DO-254 Pain Points:

Assumption: Common DO-254 formal V&V challenges like property writing, coverage closure, and CEX debug are relevant at Draper.

Question: Could Draper identify the most significant bottlenecks or time-consuming activities in their current DO-254 formal verification process that this system should prioritize alleviating?

Success Metrics (R&D):

Assumption: Success involves demonstrating improved efficiency and rigor in parts of the DO-254 formal flow.

Question: How would Draper measure success for the initial R&D phases? (e.g., % reduction in time for specific tasks like CEX analysis, quality/relevance rating of retrieved/generated properties, successful demonstration of the CEGAR loop with HITL).