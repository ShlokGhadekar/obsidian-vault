
---

## 1. Your Introduction (30–45 second version)

> "Hi, I'm Shlok, a final-year B.Tech Computer Science student with a CGPA of 8.89. I've spent the last year building production-grade systems rather than just coursework projects — I interned as an AI Engineering Intern at Bitlance Tech Hub, where I built a production AI voice agent using GPT-4, Whisper, and Twilio, deployed with Docker across GCP. Alongside that, I've built a few deep, technically differentiated portfolio projects — a deep learning customer churn system called Retentia, a real-time collaborative code editor, a concurrent key-value store in Java, and AuraOS, an AI agent for macOS with a three-tier persistent memory system and an MCP-based tool architecture. I enjoy working across the stack — from ML pipelines to systems-level concurrency to full-stack web apps and AI agent design — and I'm looking to bring that range to a product/engineering role at Razorpay."

**Brush up on:**

- Deliver this without sounding memorized — practice out loud 5–6 times, vary the wording slightly each time.
- Have a **10-second version** ready too (for when time is tight) and a **2-minute version** (if asked "tell me more").
- Know **why Razorpay** specifically — their product suite (Payment Gateway, RazorpayX, Payment Links, POS, Capital), their engineering culture, and 1–2 genuine reasons you want to work there (not generic "fintech is exciting").
- Be ready to explain **why this referral came about** honestly and briefly if asked — don't over-explain it.

---

## 2. Resume Projects — Deep Dive

For each project: know the **what**, the **why (design decisions)**, the **how (implementation details)**, and the **weak points** (so you're not caught off guard).

### A. Retentia — Deep Learning Customer Churn System

**What:** A churn/retention intelligence system built on 1M+ UCI Online Retail II transactions, comparing 5 modeling approaches (Logistic Regression, Random Forest, XGBoost, PyTorch MLP, GRU) plus a hybrid MLP+GRU fusion model. Served via FastAPI + Streamlit dashboard.

**Key talking points:**

- Leakage-safe **time-based labeling** (feature window vs. label window split) — this is a strong, non-obvious detail. Be ready to explain _why_ naive random splits leak future information in churn problems.
- Class imbalance handling (61/39 split) via class-weighted loss + **business-cost-driven threshold tuning** — be ready to explain why accuracy is the wrong metric here and how you picked the threshold.
- **SHAP explainability** — know what SHAP values actually represent (marginal contribution of each feature to a prediction, game-theoretic Shapley values) and why it matters for a churn model (stakeholders need "why," not just "who").
- GRU on 22-month behavioral sequences vs. RFM-feature MLP — be ready to explain the difference between sequence modeling and static feature modeling, and why you tried both.
- Deployment: FastAPI prediction service + Streamlit dashboard — know the difference in purpose (API for integration, dashboard for human consumption).

**Brush up on:**

- ROC-AUC vs. PR-AUC (why PR-AUC matters more under class imbalance)
- Precision/Recall/F1 tradeoffs and how threshold choice shifts them
- Basic RNN/GRU intuition (vanishing gradients, why GRU over vanilla RNN)
- SHAP vs. LIME (know SHAP is what you used, but be ready if asked to contrast)
- **Caution:** final ROC-AUC/PR-AUC/F1 numbers are still pending in your notes — don't quote made-up numbers if asked; say the comparison is still being finalized, or have real numbers ready before the interview.

---

### B. CollabEditor — Real-Time Collaborative Code Editor

**What:** Real-time collaborative code editor with JWT-over-STOMP auth and Monaco Editor. Stack: Java 17, Spring Boot 3, WebSocket/STOMP, React, Next.js, PostgreSQL.

**Key talking points:**

- Live sync across 7 languages via Monaco Editor; in-browser code execution for Java, Python, JavaScript.
- Custom **STOMP ChannelInterceptor** to propagate JWT auth to WebSocket frames — this is your strongest engineering story here. Be ready to explain: why WebSocket auth is harder than REST auth (no per-request headers by default), and how the interceptor solves it.
- Resolved **Monaco keystroke loss** via uncontrolled-mode editing with 150ms debounced sync — be ready to explain what "uncontrolled mode" means in an editor context and why debouncing was the fix (reduces network chatter, avoids cursor-jump race conditions).

**Brush up on:**

- WebSocket vs. HTTP polling vs. Server-Sent Events — know the tradeoffs
- STOMP protocol basics (it's a messaging protocol over WebSocket, pub/sub pattern)
- JWT structure and validation flow
- **Caution — do NOT overclaim:**
    - Redis was **not actually used** — if your resume or an old draft says otherwise, correct it before the interview. Don't claim Redis load testing.
    - Don't claim OT (Operational Transformation) or CRDT unless you actually implemented conflict resolution logic — if the "real-time sync" is closer to last-write-wins or debounced broadcast, say that honestly if pressed.
    - Don't claim the in-browser code execution is sandboxed/secure unless it actually is — be ready to describe the real execution mechanism if asked.

---

### C. Vaultik — Persistent Concurrent In-Memory Key-Value Store (Java)

**What:** A single-node, persistent, concurrent key-value store built specifically to demonstrate storage engine internals and concurrency control.

**Key talking points:**

- **Concurrency model:** shard array with `ReentrantReadWriteLock` per shard over a plain HashMap, with bit-mixed key routing. Be ready to explain:
    - Why lock striping beats a single global lock (reduces contention, allows parallel reads/writes on different shards)
    - Why `ReadWriteLock` over a plain `synchronized` block (multiple concurrent readers)
    - What "bit-mixed key routing" means (spreading hash codes to avoid shard skew)
- **Pluggable LRU/LFU eviction** — know the difference (LRU = recency-based, LFU = frequency-based) and when each is preferable.
- **Append-only WAL (Write-Ahead Log) with replay** — this is a classic durability mechanism. Be ready to explain: why append-only (fast sequential writes), how replay reconstructs state on restart, and the tradeoff (WAL grows unbounded without compaction — mention this as a known limitation/future work).
- **JMH benchmarking** — know that JMH (Java Microbenchmark Harness) is used because naive Java benchmarking is unreliable (JIT warmup, dead code elimination). Have your throughput/p99 latency numbers ready to quote.

**Brush up on:**

- Java concurrency primitives: `synchronized`, `ReentrantLock`, `ReentrantReadWriteLock`, `volatile`, `AtomicInteger`/`AtomicReference`
- HashMap internals (why plain HashMap isn't thread-safe, how `ConcurrentHashMap` differs from your custom sharding approach — be ready to justify why you rolled your own instead of using ConcurrentHashMap)
- Write-ahead logging and crash recovery concepts (used in real databases like PostgreSQL, Redis AOF)
- Cache eviction policies beyond LRU/LFU (e.g., ARC, Clock) — not required but good if asked "what else could you use?"
- This project is explicitly designed for **SDE-style interviews** — expect deep technical probing here if the role leans backend/systems.

---

### D. AuraOS — AI-Powered macOS Agent

**What:** An AI agent for macOS with three-tier memory (SQLite, ChromaDB, in-process), MCP (Model Context Protocol) architecture, Groq LLMs, Electron UI, and GitHub/Calendar/browser integrations.

**Key talking points:**

- **Three-tier memory system** — be ready to explain why one memory layer isn't enough: SQLite for structured/durable data, ChromaDB for semantic vector search (long-term contextual recall), in-process for fast/ephemeral state.
- **MCP architecture** — 5 independent FastAPI servers with real write capabilities (GitHub PR creation, Swift/EventKit calendar writes). Know what MCP (Model Context Protocol) is at a high level — a standard way for LLM agents to call external tools/services.
- **Engineering challenge — SQLite multi-writer lock contention:** you had 5 MCP server processes writing to SQLite concurrently, hit lock contention, and resolved it by redesigning to a **single-writer architecture**. This is a great "tell me about a hard bug" story — be ready to walk through: symptom → diagnosis → why single-writer fixed it → what you'd do differently at scale (e.g., a proper DB server instead of SQLite).
- LLM stack: Groq's llama-3.3-70b for planning, llama-3.1-8b-instant for classification — be ready to explain why you'd split tasks across a large and small model (cost/latency tradeoff — use the cheap fast model for simple classification, reserve the expensive model for complex planning).
- Built across 3 phases — good if asked about how you structure/scope a large solo project.

**Brush up on:**

- SQLite limitations (single-writer by design, not built for high-concurrency multi-process writes) — this is exactly what you hit, so know it cold.
- Vector databases and embeddings basics (what ChromaDB does, what a vector search actually returns)
- Basic LLM concepts: context window, tool-calling/function-calling, prompt vs. system prompt
- Playwright basics (browser automation) since it's mentioned in Phase 3

---

## 3. Skills Summary (What You Should Be Ready to Defend)

|Category|Skills|Brush up on|
|---|---|---|
|Languages|Java, Python, JavaScript/TypeScript|OOP concepts in Java, Python idioms, JS async/await|
|Backend|Spring Boot 3, FastAPI, Express.js|REST API design, middleware, dependency injection|
|AI/ML|PyTorch, GPT-4, Whisper, SHAP, GRU/MLP|Basic DL concepts, transformer basics (even if not directly used), evaluation metrics|
|Databases|PostgreSQL, SQLite, ChromaDB|SQL joins/indexing basics, ACID properties, vector DB concept|
|Infra/DevOps|Docker, GCP|Container basics, why containerize, basic GCP services (Cloud Run, Compute Engine)|
|Real-time/Messaging|WebSocket, STOMP, n8n|Pub/sub pattern, event-driven architecture basics|
|Frontend|React, Next.js, Monaco Editor|Component lifecycle, SSR vs. CSR basics|
|Certifications (in progress)|Java SE 17 Developer Associate, AWS SAA-C03|Core Java syntax/OOP for the former; EC2/S3/IAM basics for the latter — mention these as "in progress," don't overstate completion|

**General CS fundamentals to brush up on regardless of role type:**

- Data Structures & Algorithms — arrays, hashmaps, trees, graphs, DP basics (assume at least one DSA round)
- OOP principles (encapsulation, inheritance, polymorphism, abstraction) with real examples from your projects
- Basic System Design — load balancing, caching, database scaling (horizontal vs vertical), CAP theorem basics (relevant given Razorpay is a payments company — expect questions on reliability/consistency)
- OS/DBMS/CN basics — process vs thread, deadlocks, indexing, normalization, TCP vs UDP (standard fresher-round staples in Indian tech interviews)
- **Payments-domain awareness** — since it's Razorpay: have a basic understanding of how payment gateways work (authorization, capture, settlement, webhooks, idempotency in payment APIs). You don't need deep expertise, but showing awareness signals genuine interest.

---

## 4. General Interview Flow (Mock Structure)

Most product-company interviews (especially referral-sourced ones) follow some version of this flow. Rounds and order vary, but prepare for all of these:

### Round 1 — Screening / HR or Recruiter Call (if applicable)

- Self-intro
- "Why Razorpay?"
- Salary/location/notice period expectations (know your answers in advance)
- Basic resume walk-through

**Brush up on:** Company research (funding, products, recent news), your own availability/logistics.

---

### Round 2 — Technical Screen (DSA + CS Fundamentals)

Typical format: 1–2 coding problems (easy-medium, sometimes medium-hard) + fundamentals Q&A.

**Mock questions to prepare for:**

- Solve a medium-difficulty array/string/hashmap problem, then optimize it (time/space complexity discussion)
- Explain a tree/graph traversal from scratch
- "Design a rate limiter" or "design a URL shortener" (common warm-up system design)
- OOP question: "Design a parking lot / library system" (class design)

**Brush up on:** LeetCode medium problems (arrays, strings, hashmaps, two-pointers, sliding window, trees, basic DP), time/space complexity analysis, writing clean code under time pressure while narrating your thought process out loud.

---

### Round 3 — Project Deep Dive / Technical Discussion

This is where your Retentia, CollabEditor, Vaultik, and AuraOS knowledge from Section 2 gets tested directly.

**Mock questions to prepare for:**

- "Walk me through the hardest technical problem you solved in [project]."
- "Why did you choose [X] over [Y]?" (e.g., why ReentrantReadWriteLock over ConcurrentHashMap, why GRU over LSTM, why SQLite over Postgres for AuraOS)
- "What would you do differently if you rebuilt this?"
- "How would this scale to 10x/100x the load?"
- Follow-up probing on any number/metric you state — **only quote numbers you can defend.**

**Brush up on:** Each project's weak points (see Section 2 "Caution" notes) — have honest, thoughtful answers ready rather than getting defensive.

---

### Round 4 — System Design (if the role level warrants it)

Even for fresher/new-grad roles, expect at least a lightweight system design discussion, especially at a company like Razorpay where reliability matters.

**Mock questions to prepare for:**

- "Design a payment processing system at a high level" (walk through client → gateway → bank → webhook flow, mention idempotency and retries)
- "How would you design a notification system?"
- "How do you ensure data consistency in a distributed system?" (mention CAP theorem, eventual consistency at a basic level)

**Brush up on:** High-level architecture diagrams (practice drawing/describing boxes-and-arrows out loud), idempotency, retries/backoff, basic queueing concepts.

---

### Round 5 — Hiring Manager / Culture Fit

- "Tell me about a time you disagreed with a teammate."
- "Tell me about a time you failed and what you learned."
- "Where do you see yourself in 2–3 years?"
- Questions about working solo (most of your projects are solo) — be ready to discuss how you'd adapt to team collaboration, code review culture, etc.

**Brush up on:** 2–3 STAR-format (Situation-Task-Action-Result) stories from your internship or project work — reuse the AuraOS SQLite lock contention story here as a strong "overcame a technical challenge" example.

---

### Final — Your Questions for Them

Always have 2–3 genuine questions ready, e.g.:

- "What does the onboarding process look like for new engineers on the team?"
- "What's the biggest technical challenge the team is currently working through?"
- "How is the engineering team structured around [specific Razorpay product]?"

---

## 5. Final Checklist Before the Interview

- [ ] Confirm exact numbers/metrics for Retentia (don't leave them as "TBD")
- [ ] Correct any resume inconsistency around CollabEditor's Redis claim
- [ ] Practice the intro out loud at least 5 times
- [ ] Do 3–5 fresh LeetCode medium problems in the 48 hours before
- [ ] Re-read your own GitHub READMEs for all 4 projects the morning of
- [ ] Prepare 2–3 questions to ask the interviewer
- [ ] Research Razorpay's specific products and recent news