# Requirements Specification: ReliefOps QA (Disaster Relief & Volunteer Operations Question-Answering Assistant)
Version: 1.0

## Section 0: Project Framing & Application Scope Checkpoint

| Element | Content |
| --- | --- |
| **Problem Evidence** | **Evidence 1:** HumAID (Alam et al., ICWSM 2021) shows that active disasters generate very large volumes of unstructured crisis messages that human coordinators must classify and cross-reference against relief procedures. **Evidence 2:** Flood risk management studies in Malaysia (ResearchGate, 2020) report coordination hindrances among flood-related agencies, including multi-agency procedures and Pusat Pemindahan Sementara (PPS) processes that volunteers find difficult to navigate in static multi-page policy documents. |
| **Existing Approaches** | **1. Manual SOP handbooks and static government or IFRC portals:** provide authoritative procedures but require manual searching across multiple PDFs. **2. Crisis informatics benchmarks (HumAID, CrisisBench):** classify short crisis messages into categories but do not generate procedural answers from SOP documents. **3. Ungrounded general-purpose LLMs and generic RAG (Lewis et al., NeurIPS 2020):** answer fluently, but without a domain-specific knowledge base they may hallucinate shelter rules and evacuation protocols. |
| **Project / Engineering Gap** | Existing crisis informatics solutions perform static message classification without interactive procedural retrieval, while general-purpose LLMs lack strict grounding in approved disaster relief SOPs and deterministic safeguards against unsafe medical or life-safety advice. |
| **Objective O1** | To design a document-grounded question-answering assistant for disaster relief volunteers based on official emergency SOPs and verified crisis response datasets. |
| **Objective O2** | To develop the proposed RAG solution with semantic retrieval, source citations, and safety guardrails that refuse high-risk medical diagnosis and autonomous rescue dispatch queries. |
| **Objective O3** | To evaluate the solution in terms of retrieval precision, answer groundedness (faithfulness), response latency, and safety guardrail refusal accuracy. |
| **Intended Users** | NGO student volunteers, community relief coordinators, and logistics mobilization teams supporting non-clinical disaster relief operations. |
| **Supported Tasks** | (1) Answering questions about SOPs for evacuation shelter (PPS) registration and operation. (2) Answering questions about supply handling and triage categorisation guidelines. (3) Clarifying volunteer roles and multi-agency coordination procedures. (4) Providing source references for every document-grounded answer. |
| **Information / Knowledge Domain** | Publicly available Malaysian and international flood and disaster relief SOPs and manuals, limited to non-clinical logistics, shelter management, and volunteer coordination. |
| **Operating Context** | A web-based chat interface used by volunteers on mobile or laptop devices during flood mobilisation and preparation, where quick answers are needed and the user may be under time pressure. |
| **Important Constraints** | Only publicly available documents are used. The project operates under a student budget and semester timeline. The system is not integrated with official agency systems. The knowledge base contains a small set of SOP PDFs (2 to 3 for V1). Queries may mix English and Malay terminology. |
| **Explicit Out-of-Scope Tasks** | Clinical medical diagnosis or treatment advice. Autonomous rescue dispatch or emergency response decisions. Issuing evacuation orders or flood warnings. Real-time flood forecasting. Classification of incoming crisis reports. Replacement of official emergency services (999). |
| **Evaluation Focus** | Does the system retrieve the correct SOP content, produce answers that are faithful to and cited from approved sources, respond fast enough for field use, and reliably refuse or redirect out-of-scope and life-safety queries? |

## Section 1: Functional Requirements

| # | Requirement: The system SHALL | Scope Boundary: SHALL NOT (where relevant) |
| --- | --- | --- |
| **FR1** | Answer questions about PPS registration, setup, and operating procedures using the approved SOP documents. | Invent shelter rules, registration steps, or contact details not supported by the source documents. |
| **FR2** | Answer questions about supply handling and triage categorisation guidelines contained in the approved SOP documents. | Decide supply allocation or prioritisation for a specific real-world situation. |
| **FR3** | Provide a source reference (document name and section or page) for every answer based on the SOP documents. | Present unsupported information as if it came from an approved source. |
| **FR4** | State clearly that sufficient supporting evidence was not found when the SOP documents do not contain the answer. | Generate a speculative answer from general model knowledge in place of that statement. |
| **FR5** | Refuse clinical medical diagnosis, treatment, and rescue dispatch queries, and redirect the user to official emergency services (999). | Provide medical diagnosis, treatment advice, or dispatch instructions. |
| **FR6** | Answer queries written in English or in Malay disaster terminology (for example, Pusat Pemindahan Sementara or PPS) in clear, concise language suitable for volunteers. | - |

## Section 2: Non-Functional Requirements

| # | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
| --- | --- | --- | --- | --- | --- |
| **NFR1** | Performance | The system shall keep interactive responses within a latency suitable for field use. | Measure end-to-end latency over a defined test workload of at least 30 queries. | p90 latency | < 5,000 ms |
| **NFR2** | Correctness | Retrieved evidence and answers shall be consistent with the approved SOP documents. | Score a curated evaluation set of at least 30 question-answer pairs, checking whether the correct SOP passage appears in the top 3 results and whether the answer matches the reference. | Retrieval precision@3; correct response rate | precision@3 >= 80%; correct response rate >= 80% |
| **NFR3** | Cost | Inference cost shall remain within the student project budget. | Record model and API usage over the evaluation workload. | Cost per 100 queries | < US$0.50 per 100 queries (illustrative) |
| **NFR4** | Maintainability | Updated or newly added SOP documents shall be incorporated without rebuilding unrelated components. | Run a controlled document-update test by adding one new SOP and re-testing a related query. | Update time | < 15 minutes |

## Section 3: Responsible AI Requirements

| # | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
| --- | --- | --- | --- | --- | --- |
| **RAI1** | Hallucination of operational procedures | Answers shall be grounded in retrieved SOP content, and the system shall indicate when supporting evidence cannot be found. | Evaluate answers on a curated test set, including at least 10 questions with no answer in the documents, by checking each claim against the cited source. | Faithfulness (groundedness) score; abstention rate on unanswerable questions | Faithfulness >= 90%; abstention >= 90% |
| **RAI2** | Safety / out-of-scope medical and rescue triage | The system shall refuse medical diagnosis and rescue dispatch queries and redirect users to official emergency services (999). | Run a safety test set of at least 20 out-of-scope and life-safety queries. | Refusal-and-redirect success rate; number of unsafe compliant responses | >= 95% success; 0 unsafe responses |
| **RAI3** | Fairness and regional terminology bias | Retrieval and answer quality shall not differ beyond the agreed tolerance between local Malaysian terminology queries and international terminology queries. | Evaluate a stratified test set containing local queries (for example, PPS) and international equivalents, and compare correctness rates. | Absolute correctness-rate gap between query groups | <= 10 percentage points |
| **RAI4** | Transparency / source verifiability | The system shall provide a verifiable source reference for every document-grounded answer. | Inspect document-grounded responses for citation presence and verify that the cited passage supports the answer. | Citation presence rate; citation-source match rate | 100% presence; >= 95% match |

## Reflection

The most difficult Responsible AI requirement to measure is RAI3 (fairness and regional terminology bias). Local disaster terminology such as PPS is under-represented in global crisis datasets, so a balanced and representative test set of Malay and local queries must be built manually, and the small number of available documents limits how reliably a gap between query groups can be measured. RAI1 is also difficult to enforce technically because faithfulness judgements depend on human or model-based rubrics rather than deterministic checks.
