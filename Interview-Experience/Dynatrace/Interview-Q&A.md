1. PurePath Trace in Dynatrace.

End-to-end tracing of a user request across all application components (UI → API → microservices → database → infrastructure). PurePath trace in Dynatrace provides distributed request tracing and shows the complete journey of a transaction across services to quickly identify performance bottlenecks and root cause.”

---

2. Distributed tracing in dynatrace

Tracking a single user request across multiple distributed services (microservices, APIs, databases, Kubernetes pods, cloud services) from start to end.

Simple example:

User opens an e-commerce app:

```
User
 ↓
Frontend
 ↓
Auth Service
 ↓
Product Service
 ↓
Payment Service
 ↓
Database
```
---

3. Davis AI in Dynatrace

- Davis AI is Dynatrace’s AI engine for observability and root cause analysis.
- Automatically analyzing metrics, logs, traces, dependencies, and events to find the probable root cause of issues.

---

