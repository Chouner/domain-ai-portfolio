# Technology Scoring Model

Each collected technique is scored before it can enter the experiment backlog.

| Dimension | Score | Measurement |
|---|---:|---|
| Scenario fit | 1-5 | Match with current acoustic/AI processing problems |
| Evidence strength | 1-5 | Paper quality, code availability, benchmark quality, reproducibility |
| Technical maturity | 1-5 | Research idea, prototype, reusable library, production-ready |
| Integration cost | 1-5 | Data, compute, dependency, interface, and maintenance cost; lower cost gets higher score |
| Validation clarity | 1-5 | Whether success can be measured with existing datasets and metrics |
| Risk | 1-5 | License, patent, hallucination, safety, and reliability risk; lower risk gets higher score |
| Reusability | 1-5 | Can be reused across multiple modules or projects |

## Decision Rule

| Weighted Score | Decision |
|---:|---|
| >= 4.2 | experiment now |
| 3.5-4.2 | reserve and monitor |
| 2.8-3.5 | review manually |
| < 2.8 | reject or archive |