# 📘 SAGA PATTERN – ARCHITECT NOTES (IT GOVERNANCE & PRINCIPLES)

> Source: Building Microservices – Sam Newman  
> Topic: Sagas (Distributed Transaction Alternative)

---

## 🧠 1. Core Concept

### What is a Saga?
A **Saga** is a design pattern used to manage **long-lived transactions (LLTs)** by splitting them into **multiple smaller, independent transactions**.

- Each step = local ACID transaction
- Entire flow = eventual consistency
- No global transaction / no distributed locking

### Why Sagas?
Traditional distributed transactions (e.g., 2PC) are:
- Blocking
- Not scalable
- Prone to system-wide failure

Sagas provide:
- Scalability
- Fault tolerance
- Business process visibility

---

## 🏛️ 2. Architecture Principle Alignment

### Key Principles

- **Loose Coupling** → Services operate independently
- **Single Responsibility** → Each service owns its transaction
- **Autonomy** → Services manage their own state
- **Eventual Consistency** → No global ACID guarantee
- **Explicit Business Modeling** → Workflows are first-class citizens

> Architect Insight:  
> Sagas enforce Domain-Driven Design (DDD) thinking by making business processes explicit.

---

## 🔄 3. Saga Flow Model

### Decomposition of Business Process

Instead of:
- One global transaction (locking resources)

We use:
- Step 1 → Step 2 → Step 3 → Step N  
(each is an independent transaction)

### Example: Order Fulfillment
- Reserve stock
- Process payment
- Award loyalty points
- Ship order

Each step is handled by a separate microservice.

---

## ⚠️ 4. Key Limitation (Governance Concern)

### No Global Atomicity

- Saga is NOT an ACID transaction
- Only local atomicity per step
- System may enter intermediate inconsistent states

### Governance Implication
- Define acceptable inconsistency windows
- Require strong observability and tracing

---

## 🔁 5. Failure Handling

### Recovery Models

| Type | Description |
|------|------------|
| Backward Recovery | Rollback using compensating actions |
| Forward Recovery | Retry or continue process |

---

### Compensating Transactions

- Undo previously completed steps
- Not a true rollback → semantic rollback

#### Example:
- Payment taken → refund
- Loyalty points awarded → deduct points
- Email sent → send cancellation email

> Important: You cannot “undo history,” only compensate.

---

## 🏗️ 6. Saga Design Optimization

### Workflow Ordering Principle

- Move high-risk steps earlier
- Delay irreversible actions

#### Benefits:
- Fail fast
- Reduce rollback complexity
- Minimize compensating transactions

#### Example:
- Award loyalty points only after shipping

---

## ⚙️ 7. Implementation Models

---

### 🧭 A. Orchestrated Saga (Centralized)

#### Characteristics
- Central orchestrator controls flow
- Uses request-response communication

#### Pros
- High visibility
- Easier debugging
- Centralized control

#### Cons
- Tight coupling
- Risk of "God service"
- Logic centralization

#### Governance Fit
- Best when owned by a single team
- Strong audit/control requirements

---

### 🎭 B. Choreographed Saga (Distributed)

#### Characteristics
- No central controller
- Services react to events
- Uses message brokers

#### Pros
- Loose coupling
- High scalability
- Team autonomy

#### Cons
- Hard to trace
- No central state
- Debug complexity

#### Governance Fit
- Requires strong event governance
- Standardized contracts
- Observability

---

## 🔍 8. Observability & Governance Controls

### Mandatory Controls

#### 1. Correlation ID
- Unique ID per saga
- Passed across services
- Enables tracing

#### 2. Logging & Monitoring
- Track saga state
- Step execution
- Failures

#### 3. Audit Trail
- Persist events
- Store compensation actions
- Enable compliance tracking

---

## ⚖️ 9. Orchestration vs Choreography

| Criteria | Orchestration | Choreography |
|----------|--------------|-------------|
| Team Ownership | Single team | Multiple teams |
| Complexity | Lower | Higher |
| Coupling | Higher | Lower |
| Visibility | High | Low |
| Scalability | Medium | High |

### Recommendation
- Single team → Orchestration
- Multiple teams → Choreography

---

## 🔄 10. Saga vs Distributed Transactions

### Distributed Transactions (2PC)
- Strong consistency
- High failure risk
- Poor scalability

### Saga
- Eventual consistency
- Resilient
- Scalable

> Insight: Larger distributed systems increase failure probability.

---

## 🏛️ 11. IT Governance Considerations

### Policy Controls
- Define compensation strategies
- Retry policies
- Timeout thresholds

### Design Standards
- Idempotency enforcement
- Event schema versioning
- Standard error handling

### Risk Management
- Identify partial failures
- Handle data inconsistency
- Plan compensation limitations

---

## 📌 12. Architect Key Takeaways

### Strategic
- Sagas = Business process modeling tool
- Enables Domain-Driven Design
- Trade-off: consistency vs scalability

### Practical Rules
- Design compensations first
- Always use correlation IDs
- Prefer choreography for scale
- Avoid distributed transactions
- Optimize workflow ordering

---

## 🧩 13. Final Thought

Explicitly modeling business processes makes systems:
- More understandable
- More scalable
- More resilient

---