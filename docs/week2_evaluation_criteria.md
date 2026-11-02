# Evaluation Criteria: Cross-Border Travel & Vehicle Entry Assistant
Version: 1.0

| Objective / Requirement ID | Evaluation Criterion | Capability Bucket | Measurement Method | Metric | Threshold | User / Business Outcome |
| --- | --- | --- | --- | --- | --- | --- |
| O3 / FR1 / FR2 / NFR2 | Answer Correctness | Domain-specific capability | Evaluate a curated set of at least 50 supported queries on documents, permits, insurance, and fees. Compare each answer against the approved source documents using a defined rubric (correct, partially correct, incorrect). | Correct response rate (%) | >= 80% | Travellers receive sufficiently reliable information to prepare the correct documents before the trip. |
| O1 / FR3 / RAI2 | Source Citation and Support | Instruction-following capability | Inspect document-grounded responses for citation presence, then manually verify whether the cited source passage supports the stated answer. | Citation presence rate; citation-source match rate | 100% presence; >= 95% match | Travellers can verify requirements against official sources before relying on them. |
| O2 / FR5 / RAI1 | Out-of-Scope Refusal | Instruction-following capability | Test the system with a defined set of at least 20 out-of-scope queries (legal advice, approval guarantees, visa decisions, real-time border conditions). | Refusal-and-redirect success rate | >= 95% | Reduces the risk that users treat the system as a legal or official authority. |
| O2 / FR4 | Insufficient-Evidence Handling | Instruction-following capability | Test the system with at least 15 supported-domain queries whose answers are deliberately absent from the document store. | Rate of responses that state insufficient evidence instead of guessing | >= 90% | Prevents fabricated requirements that could lead to denied entry or penalties. |
| O3 / NFR1 | Response Latency | Cost and latency | Measure end-to-end response time across a defined workload of 50 queries. | p90 latency | < 5,000 ms | Users receive sufficiently responsive interaction during trip planning. |
| O1 / FR6 | Response Clarity | Generation capability | Rate a sample of 30 responses using a simple three-point rubric (clear, understandable with effort, unclear) applied by two reviewers. | Mean clarity score; percentage of responses rated clear | >= 80% rated clear | General travellers can understand the requirements without specialist knowledge. |

## Reflection

The most important criterion for determining usefulness is Answer Correctness. Vehicle entry requirements have direct practical consequences, because an incorrect answer about a required permit or insurance document may result in denied entry, fines, or a wasted trip. The other criteria, such as citation support and refusal behaviour, are valuable only if the underlying answers are correct in the first place.
