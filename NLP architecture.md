# NLP Architecture Documentation

## System Architecture

The proposed NLP system follows an event-driven, multi-agent architecture where multiple specialized agents process customer events in parallel and their findings are progressively synthesized, validated, and converted into actions.

### Architecture Flow

```text
┌─────────────────────────────────────┐
│     Multi-Source Event Stream       │
└──────────────────┬──────────────────┘
                   ↓
┌─────────────────────────────────────┐
│ Streaming Ingestion + Live          │
│ Transforms                          │
└──────────────────┬──────────────────┘
                   ↓
┌─────────────────────────────────────┐
│ Per-Customer Shared State Board     │
│ + Memory Layer                      │
└──────────────────┬──────────────────┘
                   ↓
        ┌──────────────────────────┐
        │       SWARM LAYER        │
        │  (Parallel, Independent) │
        ├──────────────────────────┤
        │ • Usage/Engagement Agent │
        │ • Support/Sentiment Agent│
        │ • Transaction/Billing    │
        │   Agent                  │
        │ • KYC/Compliance Agent   │
        └─────────────┬────────────┘
                      ↓
              Structured Findings
                      ↓
┌────────────────────────────────────────────┐
│ Life-Event Inference Agent                 │
│ (Agent-Dependent Trigger)                  │
└──────────────────────┬─────────────────────┘
                       ↓
┌────────────────────────────────────────────┐
│ Synthesis / Correlation Agent              │
└──────────────────────┬─────────────────────┘
                       ↓
┌──────────────────────┴─────────────────────┐
│                                            │
↓                                            ↓
┌───────────────────────┐        ┌───────────────────────┐
│ Offer / Eligibility   │        │ Retention / Action    │
│ Agent                 │        │ Agent                 │
└───────────┬───────────┘        └───────────┬───────────┘
            └────────────────┬───────────────┘
                             ↓
              ┌──────────────────────────┐
              │ Critique / Compliance-   │
              │ Refiner Agent            │
              └────────────┬─────────────┘
                           ↓
        ┌─────────────────────────────────┐
        │ Hard Guardrail Agent            │
        │ (Always Running in Parallel,    │
        │  Event-Based)                   │
        └────────────────┬────────────────┘
                         ↓
              ┌──────────────────────────┐
              │ Human-in-the-Loop        │
              │ Checkpoint               │
              └────────────┬─────────────┘
                           ↓
        ┌────────────────────────────────────┐
        │ Action Execution / No-Action Log   │
        └────────────────┬───────────────────┘
                         ↓
                ┌─────────────────┐
                │ Feedback into   │
                │ Memory          │
                └─────────────────┘
```
<img width="1878" height="600" alt="Screenshot 2026-09-15 204902" src="https://github.com/user-attachments/assets/77ae24d1-9602-49cd-8151-3125060df1cf" />


