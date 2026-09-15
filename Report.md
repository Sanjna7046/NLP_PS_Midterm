# Understanding of the Domain
Traditional Customer 360 systems solve only the data-aggregation problem. They create unified customer profiles and dashboards, but remain passive. Humans must still log in, interpret signals, and decide when to act. At scale this is impossible. Subtle, compounding patterns — a quiet drop in engagement combined with negative support sentiment and unusual transaction behaviour — are routinely missed until the customer has already churned or a high-value opportunity has closed.
The real problem is therefore not visibility but continuous, autonomous reasoning and action. The required system must be ambient (always running in the background), event-driven, and capable of maintaining an evolving understanding of each customer’s current life phase while deciding on concrete interventions from a bounded action set.
Key Insights from Research
## Three insights shaped the design:

1.Multi-agent systems deliver clear gains only when the right coordination pattern is chosen for each stage. Parallel “swarm” works well for independent signal gathering; sequential handoffs and critique-refiner patterns are better for high-stakes decision making.

2.Memory must be deliberate. Working, episodic and semantic memory are all required, but they should be shared through a structured per-customer state board rather than private agent histories. This prevents context pollution and memory leakage while allowing life-event inferences to persist over time.

3.Production safety cannot be prompt-based. Hard deterministic guardrails, full traceability, explainability as a first-class output, and real human-in-the-loop checkpoints at irreversible actions are non-negotiable architectural components.

## Approach
The solution is a hybrid multi-agent architecture organised in clear layers:

‣ A streaming ingestion layer continuously accepts multi-source events and applies live transforms (rolling aggregates, anomaly scores, sentiment).

‣ Four specialist swarm agents (Usage, Support, Transaction, KYC) run in parallel and publish only structured findings to a shared per-customer state board.

‣ An agent-dependent Life-Event Inference agent maintains an evolving picture of the customer’s current life phase inside episodic memory.

‣ A Synthesis agent reconciles findings, after which Offer and Action agents propose a concrete intervention (or explicit no-action).

‣ A Critique-Refiner agent reviews the proposal for cost, tone and compliance.

‣ A parallel Hard Guardrail agent can instantly bypass the entire pipeline on high-risk signals.

‣ Any customer-facing or high-cost decision stops at a real Human-in-the-Loop checkpoint that includes the full explanation and source citations.
