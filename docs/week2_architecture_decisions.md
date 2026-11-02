# Architecture Needs, Decisions and Traceability: Cross-Border Travel & Vehicle Entry Assistant
Version: 1.0

## Section 1: Architecture Needs from Requirements

| Requirement ID | What does the requirement need? | Architecture Response |
| --- | --- | --- |
| FR1 / FR2 / NFR2 | Answers must be factually consistent with official entry requirements. | Knowledge source of official documents and a knowledge access component |
| FR3 / RAI2 | Answers must include verifiable source references. | Source tracking and a grounding and citation check |
| FR4 | The system must state when evidence is insufficient. | Grounding check that can return an insufficient-evidence response |
| FR5 / RAI1 | Out-of-scope queries must be refused and redirected. | Scope / safety control placed before knowledge access |
| RAI3 | User identity and plate numbers must not reach external model APIs. | Privacy filter placed before the language model service |
| NFR1 | Responses must meet the latency threshold. | Short interaction path with lightweight controls before the language model service |
| NFR4 / NFR5 | Documents must be updated and kept current without rebuilding other components. | Separate document update process feeding the knowledge source |
| RAI4 | Performance must not differ across query languages. | Language-neutral interface and an evaluation point using a stratified test set |
