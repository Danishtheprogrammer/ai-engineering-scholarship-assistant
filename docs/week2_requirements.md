# Requirements Specification: Cross-Border Travel & Vehicle Entry Assistant
Version: 1.0

## Section 0: Project Framing & Application Scope Checkpoint

| Framing element | Description |
| --- | --- |
| Problem Evidence | Travellers who drive across borders must comply with vehicle entry rules (permits, insurance, road tax, required documents, fees) that are published across several agency websites and change over time. [Source 1: official agency page showing the requirement]. [Source 2: news report or traveller-forum evidence of confusion or penalties for non-compliance]. |
| Existing Approaches | (1) Official government portals and apps ([e.g. JPJ, LTA, ICA, customs pages]): authoritative, but information is fragmented and not conversational. (2) General-purpose LLM chatbots: fluent and easy to use, but not grounded in official documents and may give outdated or invented requirements. (3) Travel blogs and community forums: practical and experience-based, but unverified and often outdated. |
| Project / Engineering Gap | No single conversational tool consolidates current official vehicle entry requirements and provides verifiable source references, while declining to give legal or approval decisions. The remaining limitation is source traceability and grounding, not scientific novelty. |
| Objective O1 | To design a document-grounded assistant that answers vehicle entry questions using official source documents and provides source references. |
| Objective O2 | To develop a V1 prototype that supports defined query types and declines out-of-scope queries with an appropriate redirect. |
| Objective O3 | To evaluate the prototype on answer correctness, source support, scope refusal, and response latency using a curated test set. |
| Intended Users | Private travellers who plan to cross the border by road in a personal vehicle. |
| Supported Tasks | Answering questions on required documents, vehicle permits, insurance, applicable fees and tolls, and general entry procedures for vehicles. |
| Information / Knowledge Domain | Official vehicle entry and border-crossing requirements for [country pair / corridor], based on publicly available government documents. |
| Operating Context | Web chat interface used during pre-trip planning on a phone or laptop. It is not intended for real-time use at the border checkpoint. |
| Important Constraints | Only publicly available official documents are used. Rules change, so each document carries a retrieval date. Limited budget (free-tier or open models, Hugging Face and Kaggle resources) and a single-semester timeline. |
| Explicit Out-of-Scope Tasks | Legal advice, guaranteeing entry or permit approval, submitting applications or payments, visa and immigration decisions, real-time traffic or queue information, and handling personal identity documents. |
| Evaluation Focus | Does the system give correct, source-supported answers to supported queries, refuse out-of-scope queries appropriately, and respond within an acceptable time? |

## Section 1: Functional Requirements

| # | Requirement: The system SHALL | Scope Boundary: SHALL NOT (where relevant) |
| --- | --- | --- |
| FR1 | Answer questions about documents, permits, and insurance required for a vehicle to enter [destination country] from the supported documents. | Invent requirements, fees, or permit names that are not supported by the source documents. |
| FR2 | Answer questions about applicable fees, tolls, and validity periods for vehicle entry where these are stated in the source documents. | Calculate or promise exact charges for a specific traveller beyond what the documents state. |
| FR3 | Provide a source reference (document title and section or URL) for every answer based on official documents. | Present unsupported information as if it came from an official source. |
| FR4 | State when the available documents do not contain sufficient information to answer, and indicate the retrieval date of the source used. | Guess an answer when evidence is missing. |
| FR5 | Decline queries outside the defined scope and provide an appropriate redirect to the relevant official authority. | Provide legal advice, confirm entry approval, or make visa and immigration decisions. |
| FR6 | Present answers in clear language appropriate for general travellers. | - |

## Section 2: Non-Functional Requirements

| # | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
| --- | --- | --- | --- | --- | --- |
| NFR1 | Performance | The system shall respond to interactive queries within the agreed latency target. | Measure end-to-end latency on a defined test workload of 50 queries. | p90 latency | < 5,000 ms |
| NFR2 | Correctness | Answers shall be factually consistent with the approved source documents. | Score a curated evaluation set of at least 50 supported queries using a defined rubric. | Correct response rate (%) | >= 80% |
| NFR3 | Cost | Inference cost shall remain within the project budget. | Record model and API usage over the evaluation workload. | Cost per 100 queries | < US$0.50 |
| NFR4 | Maintainability | Updated official documents shall be incorporated without rebuilding unrelated components. | Run a controlled document-update test. | Update time | < 10 min |
| NFR5 | Reliability | Source documents used by the system shall be kept current. | Audit the document store against the official websites. | Percentage of documents verified within the last 30 days | 100% |

## Section 3: Responsible AI Requirements

| # | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
| --- | --- | --- | --- | --- | --- |
| RAI1 | Safety / Scope (users may treat answers as legal or approval decisions) | The system shall refuse out-of-scope queries and redirect users to the relevant official authority. | Run a safety and scope test set containing at least 20 out-of-scope queries. | Refusal-and-redirect success rate | >= 95% |
| RAI2 | Transparency / Grounding (users cannot verify where an answer came from) | The system shall provide a verifiable source reference for document-grounded answers and indicate when sufficient evidence cannot be found. | Inspect responses for citation presence and check whether the cited source supports the answer. | Citation presence rate; citation-source match rate | 100% presence; >= 95% match |
| RAI3 | Privacy (users may enter passport, identity, or plate numbers) | The system shall not store or transmit user identity document numbers or vehicle plate numbers to external model APIs. | Run privacy test cases containing sample identifiers and inspect logs and outgoing payloads. | Number of detected identifier leaks | 0 |
| RAI4 | Fairness (users may query in different languages or phrasing styles) | System performance shall not differ beyond the agreed tolerance across query languages. | Evaluate a stratified test set in English and Malay and compare correctness rates. | Absolute correctness-rate gap between languages | <= 10 percentage points |

## Reflection

The Responsible AI requirement that is most difficult to measure is RAI2 (citation-source match). Checking that a cited source genuinely supports the answer requires manual judgement for each response, because the system may cite a correct document but a passage that does not contain the stated requirement. This makes automated enforcement unreliable and limits the evaluation to a small, manually reviewed sample.
