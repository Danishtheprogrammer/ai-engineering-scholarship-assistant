# Architecture Decisions & Traceability: ReliefOps QA
Version: 1.0

## Step C1: Simplest Architecture

User Query → AI Application → AI Capability (Language Model Service) → Response

This simple flow cannot satisfy the Part A requirements on its own. A language model without access to approved sources cannot provide grounded answers, source references, or reliable abstention, and it has no control that refuses life-safety queries. Additional components are therefore introduced only where a requirement justifies them.

## Step C2: Architecture Needs from Requirements

| Requirement ID | What does the requirement need? | Architecture Response |
| --- | --- | --- |
| FR1 / FR2 / NFR2 | Answers about PPS and supply procedures must match approved source content and meet the correctness threshold. | Knowledge Source and Knowledge Access (retrieval of relevant passages) |
| FR3 / RAI4 | Every document-grounded answer must include a verifiable source reference. | Source tracking in Knowledge Access and a Citation Check before output |
| FR4 / RAI1 | The system must not invent procedures and must state when evidence is insufficient. | Output Grounding & Citation Check with an insufficient-evidence response path |
| FR5 / RAI2 | Medical diagnosis and rescue dispatch queries must be refused and redirected to 999. | Input Scope / Safety Control before retrieval and generation |
| FR6 / RAI3 | English and Malay terminology queries must receive comparable answer quality. | Knowledge Access that combines exact keyword matching and semantic matching to handle local terminology (for example, PPS) |
| NFR1 | Responses must meet the latency threshold. | A short, linear interaction path with a minimal number of processing stages |
| NFR4 | New documents must be added without rebuilding unrelated components. | Knowledge Source kept separate from the Language Model Service |

## Step C4: Architecture Decision Record

| Decision | Requirement / Constraint | Alternatives Considered | Selected Option | Trade-off Accepted |
| --- | --- | --- | --- | --- |
| Include knowledge access with hybrid retrieval over approved sources | FR1, FR3, NFR2, RAI1, RAI4, O2: answers must be grounded and traceable, and local terms such as PPS need exact keyword matching as well as semantic matching | Base model only; keyword-only retrieval; dense-only retrieval; hybrid (keyword plus dense) retrieval | Hybrid knowledge access feeding the Language Model Service | More system complexity and added retrieval latency, but better handling of exact local terminology and paraphrased queries |
| Include an explicit input scope and safety control | FR5, RAI2: medical diagnosis and rescue dispatch queries must be refused and redirected to 999 | Instruction in the model prompt only; explicit scope-control component | Explicit scope and safety control before retrieval and generation | Possible false refusals of legitimate queries and additional processing time |
| Include an output grounding and citation check | FR4, RAI1, RAI4: unsupported answers must not reach volunteers | No output check; rely on the model following instructions; explicit check of answer against retrieved passages | Explicit output grounding and citation check with an insufficient-evidence path | Additional latency (affects NFR1) and occasional rejection of correct but weakly supported answers |

## Initial Requirements Traceability Matrix

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence |
| --- | --- | --- | --- | --- |
| O1 | FR1 / FR2 / NFR2 | Retrieval and Answer Correctness | Knowledge Source and Knowledge Access | Retrieval precision@3 and correct response rate on the curated question set |
| O1 | FR3 / RAI4 | Source Citation and Support | Source tracking and Output Grounding & Citation Check | Citation presence rate and citation-source match rate on inspected responses |
| O2 | FR5 / RAI2 | Out-of-Scope Refusal and Redirect | Input Scope / Safety Control | Refusal-and-redirect success rate on the 20-query safety test set |
| O3 | NFR2 | Retrieval Baseline Comparison | Knowledge Access (hybrid keyword plus semantic retrieval) | Precision@3 of BM25-only, dense-only, and hybrid retrieval on the curated query set |
| O3 | FR4 / RAI1 | Answer Faithfulness and Abstention | Output Grounding & Citation Check | Faithfulness score and abstention rate on the answerable and unanswerable test questions |
| O3 | NFR1 | Response Latency | Linear interaction path across all components | p90 latency over the 30-query workload |

## Final Review Checklist

- Each major component supports a requirement or evaluation need (see Step C2 and the traceability matrix).
- Evaluation points are marked at retrieval, output checking, scope control, and the end-to-end flow.
- No specific model, vector database, guardrail framework, or UI framework has been committed to at V1.
- The simpler architecture (User → Application → Language Model Service → Response) was rejected because it cannot support grounding, citation, abstention, or refusal requirements.

## Reflection

The most important trade-off was the decision to add an output grounding and citation check alongside knowledge access. It was driven by RAI1 and FR4, because an invented shelter rule or supply protocol could cause logistical harm among volunteers. The accepted trade-off is additional latency, which puts pressure on the NFR1 target of p90 below 5,000 ms, and a risk that some correct but weakly supported answers are replaced by an insufficient-evidence response.
