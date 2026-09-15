# PRELIMINARY RESEARCH DOCUMENTATION


This section summarizes the key research findings that informed the design of our NLP multi-agent architecture.

---

## 1. Multi-Agent System (MAS) Coordination Patterns

| Source | Key Takeaway | How It Informed Our Architecture |
|---|---|---|
| **Google Cloud Architecture Center – “Choose a design pattern for your agentic AI system” (2025/2026)** | Clear distinction between **Swarm** (parallel independent agents), **Sequential/Handoff**, **Review-and-Critique (Generator-Critic)**, and **Coordinator** patterns. | We deliberately use **Swarm** only for the initial signal-gathering stage (Usage, Support, Transaction, KYC agents) because the signals are independent. We switch to **Handoff + Critique-Refiner** for the decision stage. |
| **Resourcifi / Multiple 2026 Surveys on Multi-Agent Topologies** | Swarm is excellent for parallelizable independent subtasks but can suffer from error amplification and lack of conflict resolution. Sequential pipelines are predictable but brittle. Critique/Debate patterns improve quality for high-stakes outputs. | Confirmed our **hybrid design: Swarm → Synthesis (Handoff) → Critique-Refiner → HITL**. We avoid using a pure mesh/swarm architecture for the entire pipeline. |
| **Zylos Research & Google Research Scaling Studies (2026)** | Multi-agent systems improve parallelizable tasks (~80% gain) but can degrade sequential reasoning tasks by 39–70%. Independent agents can amplify errors significantly. | Reinforced the decision to keep swarm agents independent and publish only **structured findings** to a shared state board, never raw reasoning. |

---

## 2. Agent Memory Architecture

| Source | Key Takeaway | How It Informed Our Architecture |
|---|---|---|
| **Park et al. – Generative Agents (2023) + CoALA Framework** | Classic memory taxonomy: **Working, Episodic, and Semantic memory**. Memory streams and reflection enable coherent long-horizon behaviour. | Directly adopted: **Working Memory** for short-term current state, **Episodic Memory** for per-customer intervention history and life events, and **Semantic Memory** for shared policies and patterns. |
| **Packer et al. – MemGPT / Letta (2023–2026)** | OS-inspired hierarchical memory with core/working and archival memory. Agents should actively manage what remains in context. | Inspired the **Shared Per-Customer State Board** as the single source of truth that all agents can read from and write structured conclusions to. This prevents private, siloed memories. |
| **Recent 2026 Papers on Governed Shared Memory for Multi-Agent LLM Systems** | Shared memory requires governance through scoped access, provenance, and temporal correctness. Blackboard/shared-state patterns are preferred over pure message passing in many multi-agent settings. | Confirmed our core design: **one shared state board per customer ID**. Agents publish only structured findings such as `login_drop: -40%, confidence: 0.91`, never full chain-of-thought. |

---

## 3. Ambient Agents vs. Chatbots & Trigger Types

| Source | Key Takeaway | How It Informed Our Architecture |
|---|---|---|
| **SupportLogic / Walturn / ZBrain Articles on Ambient Agents (2025–2026)** | Ambient agents are **event-driven, always-on, persistent-memory systems** that can act without direct human initiation. Chatbots are generally prompt-driven and session-scoped. | Directly shaped the three trigger types used in our architecture: **Event-Based** (real-time), **Time-Based** (scheduled health checks), and **Agent-Dependent** (Life-Event Inference fires only after correlated swarm signals). |

---

## 4. Streaming / Live Retrieval Freshness

| Source | Key Takeaway | How It Informed Our Architecture |
|---|---|---|
| **RisingWave / Streamkap / Timeplus – Streaming RAG Articles (2026)** | Batch RAG can become stale within hours. Streaming ingestion, live transforms, and continuous index updates are important for real-time agents. | Led us to place a dedicated **Streaming Ingestion + Live Transforms** layer at the front of the architecture, with rolling aggregates and anomaly scores computed as events arrive. |

---

## 5. Guardrails, HITL, Observability & Explainability

| Source | Key Takeaway | How It Informed Our Architecture |
|---|---|---|
| **Traversaal / MLflow / Microsoft / AWS Well-Architected Agentic AI Lens (2026)** | Production guardrails should be deterministic and layered across input, output, and execution. HITL should be a real blocking checkpoint for irreversible actions rather than merely a prompt instruction. Full trace logging of tool calls and agent handoffs is essential. | Implemented as a **Hard Guardrail Agent** that operates continuously and deterministically, with the ability to bypass the LLM pipeline. An explicit **HITL checkpoint** is placed before customer-facing or high-cost actions, alongside structured logging of the complete decision path. |

---

## Summary of Architectural Decisions

Based on the above research, our architecture follows a **hybrid multi-agent design**:

1. **Swarm Layer** → Parallel and independent signal extraction.
2. **Shared State Board** → Centralized, structured per-customer memory.
3. **Life-Event Inference** → Agent-dependent triggering based on correlated signals.
4. **Synthesis / Correlation** → Combines findings from independent agents.
5. **Offer / Retention Agents** → Generates potential actions.
6. **Critique / Compliance Refiner** → Reviews and improves proposed actions.
7. **Hard Guardrails** → Deterministic safety and compliance enforcement.
8. **Human-in-the-Loop** → Blocking checkpoint before consequential actions.
9. **Action + Feedback Loop** → Executes approved actions and feeds outcomes back into memory.

> **Design Principle:** Use parallel agents where tasks are independent, structured shared state for coordination, and layered critique + deterministic guardrails before consequential actions.



## References & Resources

The following resources were consulted during the preliminary research and architectural design of the proposed NLP multi-agent system.

### 1. Multi-Agent System (MAS) Coordination Patterns

- **Google Cloud Architecture Center — Choose a design pattern for your agentic AI system**  
  Covers sequential, parallel, review-and-critique, swarm, coordinator, and human-in-the-loop agent patterns.  
  [Google Cloud Architecture Center](https://docs.cloud.google.com/architecture/choose-design-pattern-agentic-ai-system)

- **Kim et al. — Towards a Science of Scaling Agent Systems**  
  Google Research / Google DeepMind / MIT research on how multi-agent architectures perform under different task structures, including parallel vs. sequential tasks and error amplification.  
  [Google Research](https://research.google/blog/towards-a-science-of-scaling-agent-systems-when-and-why-agent-systems-work/)  
  [arXiv Paper](https://arxiv.org/abs/2512.08296)

---

### 2. Agent Memory Architecture

- **Park et al. — Generative Agents: Interactive Simulacra of Human Behavior (2023)**  
  Introduces memory streams, retrieval, reflection, and planning for long-horizon agent behaviour.  
  [arXiv](https://arxiv.org/abs/2304.03442)

- **Sumers et al. — Cognitive Architectures for Language Agents (CoALA)**  
  Provides a framework for language-agent memory, including modular memory components and interaction with internal and external environments.  
  [arXiv](https://arxiv.org/abs/2309.02427)

- **Packer et al. — MemGPT: Towards LLMs as Operating Systems**  
  Introduces hierarchical memory management inspired by operating-system virtual memory.  
  [MemGPT Research](https://research.memgpt.ai/)

---

### 3. Ambient Agents & Trigger Types

- **SupportLogic — Ambient Agents vs. Chatbots**  
  Discusses always-on, proactive, event-driven ambient agents and their differences from conventional chatbots.  
  [SupportLogic](https://www.supportlogic.com/resources/blog/ambient-agents-vs-chatbots-why-the-future-of-enterprise-support-is-always-on-intelligence/)

- **ZBrain — Ambient Agents Explained**  
  Covers event-driven ambient agents, persistent context, triggers, workflows, guardrails, and human-in-the-loop approval.  
  [ZBrain](https://zbrain.ai/ambient-agents/)

- **Walturn — Chat Agents vs. Ambient Agents**  
  Provides a comparison between conversational agents and proactive/event-driven ambient agents.  
  [Walturn](https://www.walturn.com/insights/chat-agents-vs-ambient-agents-two-paths-to-ai-driven-assistance)

---

### 4. Streaming / Live Retrieval Freshness

- **RisingWave — Streaming for RAG: How to Keep Retrieval-Augmented Generation Fresh in Real Time**  
  Discusses streaming RAG, continuous data updates, CDC, live transformations, and maintaining fresh retrieval context.  
  [RisingWave](https://risingwave.com/blog/streaming-for-rag-real-time-retrieval-augmented-generation/)

- **RisingWave — Real-Time RAG Pipeline**  
  Covers continuously updated structured context, streaming data, incremental updates, and hybrid retrieval.  
  [RisingWave Real-Time RAG](https://risingwave.com/real-time-rag-pipeline/)

---

### 5. Guardrails, Human-in-the-Loop, Observability & Explainability

- **AWS Well-Architected Framework — Agentic AI Lens**  
  Provides architectural guidance for agent reliability, security, observability, human oversight, guardrails, and multi-agent systems.  
  [AWS Agentic AI Lens](https://docs.aws.amazon.com/wellarchitected/latest/agentic-ai-lens/agentic-ai-lens.html)

- **AWS — Human-in-the-Loop for Critical Decisions**  
  Covers risk-tiered human approval before high-risk agent operations.  
  [AWS Human-in-the-Loop Guidance](https://docs.aws.amazon.com/wellarchitected/latest/agentic-ai-lens/agentsec04-bp02.html)

- **Microsoft — Secure Autonomous Agentic AI Systems**  
  Covers deterministic guardrails, human-in-the-loop controls, logging, observability, and high-risk/irreversible actions.  
  [Microsoft Learn](https://learn.microsoft.com/en-us/security/zero-trust/sfi/secure-agentic-systems)

- **Microsoft — AI Agent Shared Responsibility Model**  
  Covers agent permissions, human approval, memory isolation, audit logging, and multi-agent trust boundaries.  
  [Microsoft Learn](https://learn.microsoft.com/en-us/azure/security/fundamentals/shared-responsibility-ai-agent)

- **MLflow — LLM & Agent Observability**  
  Covers tracing, execution visibility, tool calls, inputs/outputs, quality monitoring, and agent observability.  
  [MLflow](https://www.mlflow.org/genai/observability/)

- **MLflow — Tracing for LLM and Agent Observability**  
  Provides OpenTelemetry-compatible tracing for LLM and agent workflows.  
  [MLflow Tracing](https://mlflow.org/docs/latest/genai/tracing)

