# Bain BCN Labs — Interview Prep Q&A

Priority order: **Project deep-dives → GenAI/MCP concepts → CS/systems fundamentals → Full-stack → Behavioral → Why Bain**

---

## 1. Project Deep-Dives (highest priority — expect 15-20 min per project if it comes up)

### AuraOS (your strongest card — maps directly to "GenAI, Agentic & MCP")

**Q: Walk me through AuraOS. What does it do and why did you build it?** A: A solo AI agent that controls macOS via natural language. Built it to go beyond toy chatbot demos and actually explore agentic systems with real write capabilities — GitHub repo/PR creation, native calendar integration, browser control — plus persistent memory across sessions, which most agent demos skip.

**Q: Why 5 separate MCP servers instead of one monolithic backend?** A: Each server (e.g. GitHub, calendar, browser, memory, workflow engine) owns a distinct capability and can be developed, tested, and scaled independently. MCP standardizes how the agent core discovers and calls tools, so adding a new capability means adding a new server, not touching existing code. It also isolates failure — if the browser-control server crashes, calendar/GitHub servers keep working.

**Q: Tell me about the SQLite write-contention bug — your hardest engineering problem.** A: With 5 independent FastAPI processes all writing to the same SQLite file, I hit "database is locked" errors under concurrent access — SQLite allows one writer at a time, and multiple processes attempting writes were colliding. I redesigned to a single-writer architecture: one process owns all writes, others route write requests to it (reads stayed direct). This is a classic distributed-systems tradeoff — sacrificing some write throughput/parallelism for correctness and simplicity, which was the right call at this scale.

**Q: Explain the three-tier memory system.** A: SQLite for structured, queryable state (tasks, history); ChromaDB for semantic/vector recall — so the agent can retrieve "similar past situations" via embedding search, not just exact matches; and an in-process tier for fast, session-local working memory that doesn't need persistence. This mirrors how you'd design memory for a production RAG/agentic system: hot/fast vs. durable vs. semantic layers each serve a different retrieval pattern.

**Q: What would you improve if you kept building it?** A: Move from a single-writer SQLite bottleneck to a proper embedded DB with better concurrency (e.g., LiteFS or Postgres if it ever needed multi-machine), add evals for the agent's tool-selection accuracy, and add observability/tracing across the 5 MCP servers to debug multi-hop tool calls.

### CollabEditor (full-stack proof point — answer carefully, see caution notes)

**Q: What's the architecture?** A: React/Next.js frontend with Monaco Editor for the code surface, Spring Boot 3 backend, WebSocket/STOMP for real-time messaging, JWT for auth. Rooms are session-based; multiple users see live keystrokes across 7 languages, plus in-browser execution for Java, Python, and JavaScript.

**Q: How did you solve keystroke loss / sync conflicts?** A: Monaco in controlled mode was fighting incoming WebSocket updates and dropping local keystrokes. I switched to uncontrolled-mode editing with 150ms debounced sync — local edits apply immediately to the editor instance, and remote changes merge in on a debounce window rather than every keystroke. ⚠️ **Do not claim OT (Operational Transform) or CRDT-based conflict resolution — you didn't build that.** If pushed on "what happens if two people type in the same spot," be honest: it's last-write-wins on a debounce, not a proper CRDT merge, and that's a known limitation you'd solve with Yjs/Automerge if you extended it.

**Q: How does auth work over WebSocket?** A: Wrote a custom STOMP `ChannelInterceptor` that validates the JWT on the CONNECT frame and rejects unauthorized socket upgrades, since standard HTTP JWT middleware doesn't apply to the WebSocket handshake the same way.

**Q: Is the code execution sandboxed? Is Redis used for scaling?** A: ⚠️ Be precise: code execution is **not sandboxed** — flag this proactively as a known risk/limitation if it comes up, rather than getting caught in it. Redis was in an early draft but **wasn't actually used** in the final build — don't claim Redis load-testing or pub/sub scaling.

### Vaultik (systems/concurrency depth)

**Q: Why build a KV store from scratch?** A: To go deep on storage-engine internals and concurrency control for SDE-style interviews — things like eviction policies, WAL-based crash recovery, and lock design, which most CRUD projects don't touch.

**Q: How does the concurrency model work?** A: Shard array with a `ReentrantReadWriteLock` per shard over a plain HashMap, with bit-mixed key routing to spread keys evenly across shards. This limits lock contention to only the keys landing in the same shard, and read-write locks let concurrent reads proceed without blocking each other. I chose this over a single global lock (too much contention) or lock-free structures (too complex to reason about/justify for this scope).

**Q: How did you validate it works under load?** A: JMH benchmarking — measured throughput and p99 latency under concurrent access across varying workload patterns (read-heavy vs. write-heavy) to confirm the sharding actually reduced contention rather than just adding overhead.

**Q: Why single-node, not distributed?** A: Deliberately scoped down — a distributed KV store (replication, consensus, partition tolerance) is a multi-month project on its own. Single-node let me go deep on storage engine + concurrency + crash recovery and have a defensible, fully-understood system rather than a shallow distributed one.

---

## 2. GenAI / Agentic / MCP Concepts (JD-driven — expect conceptual questions beyond your projects)

**Q: What is RAG and when would you use it over fine-tuning?** A: Retrieval-Augmented Generation grounds an LLM's response in retrieved external documents/context at inference time, rather than baking knowledge into model weights. Use RAG when knowledge changes frequently, needs to be traceable/citable, or is too large to fit training data economically. Fine-tune when you need consistent behavior/format/style, or a capability retrieval can't provide (e.g., a specific tone, structured output habits).

**Q: What is MCP (Model Context Protocol) and why does it matter?** A: A standardized protocol for how an LLM/agent discovers and calls external tools/servers — instead of every integration being a bespoke function-calling schema, MCP gives a common interface, so tools are interoperable across agents. I used it directly in AuraOS — each capability (GitHub, calendar, browser) is its own MCP server the agent core calls into.

**Q: What's the difference between an agentic workflow and a simple LLM call?** A: A single LLM call is one-shot: prompt in, completion out. An agentic workflow lets the model plan multi-step actions, call tools, observe results, and decide the next step — potentially looping, retrying, or branching based on intermediate outcomes. AuraOS's workflow engine is exactly this: it decomposes a natural-language instruction into a sequence of MCP tool calls.

**Q: What's a multi-agent system, and when is it better than a single agent?** A: Multiple specialized agents (e.g., a planner, a coder, a reviewer) collaborate, each with a narrower scope than one generalist agent. Better when tasks benefit from separation of concerns, parallel work, or an adversarial/review step (one agent checks another's output) — worse when the coordination overhead outweighs the benefit for a simple task.

**Q: How would you evaluate an LLM-based feature (evals)?** A: Define task-specific success criteria up front (exact match, semantic similarity, human/LLM-as-judge rubric), build a held-out test set that reflects real usage, and track regressions across prompt/model changes — not just eyeballing a few outputs. For agentic systems specifically, you'd also eval tool-selection accuracy and task-completion rate, not just final text quality.

**Q: What's the difference between LangGraph-style orchestration and a simple chain?** A: Chains are linear/DAG pipelines. LangGraph-style orchestration models the workflow as a stateful graph, supporting cycles/loops (retry, replan) and conditional branching based on agent state — better fit for real agentic behavior than a fixed pipeline.

---

## 3. CS Fundamentals / Systems

**Q: Explain the CAP theorem / a relevant tradeoff you've made.** A: Pivot to the AuraOS single-writer SQLite decision — chose consistency/correctness over write availability/throughput under concurrent access.

**Q: Explain read-write locks vs. mutexes.** A: A mutex allows only one thread in at all, for reads or writes. A read-write lock allows many concurrent readers OR one exclusive writer — better throughput when reads dominate, which is the case in most KV-store workloads (Vaultik's design rationale).

**Q: Overfitting vs. underfitting / bias-variance tradeoff.** A: Underfitting/high bias: model too simple, misses real patterns, poor on train and test. Overfitting/high variance: model too complex, memorizes training noise, great on train, poor on test/generalization. Managed via regularization, more data, cross-validation, simpler architectures.

**Q: What is a WAL (write-ahead log) and why use one?** A: Every write is appended to a durable log before it's applied to in-memory state. On crash, replay the log to reconstruct state — gives durability without requiring every write to be a full disk-synced state snapshot. Used this in Vaultik for crash recovery.

**Q: DSA — expect 1-2 live problems.** Standard prep: arrays/strings, hashmap-based problems, two pointers, BFS/DFS on graphs/trees, and one DP problem. Given time constraints tonight, review patterns rather than grind new problems — you've done 150+ on LeetCode, trust the reps.

---

## 4. Full-Stack / Cloud

**Q: How would you deploy a FastAPI + React app on Azure/AWS with CI/CD?** A: Containerize both with Docker, push images via CI (GitHub Actions) on merge to main, deploy backend as a container app/ECS or Azure Container Apps service behind a load balancer, frontend to static hosting/CDN (S3+CloudFront or Azure Static Web Apps), with environment-based config and automated tests gating the deploy.

**Q: What's your experience with cloud-native deployment?** A: AuraOS services run via Docker; Bitlance internship work was deployed via Docker orchestrating APIs across Google Cloud, Twilio, and CMS endpoints. Comfortable with containerization and API orchestration; AWS SAA-C03 and AWS AI Practitioner certifications back the cloud fundamentals even where hands-on production AWS experience is still growing.

**Q: How do you approach API design (REST)?** A: Resource-oriented URLs, correct HTTP verbs/status codes, versioning, and stateless auth (JWT) — as implemented in CollabEditor's session-based room APIs.

---

## 5. Behavioral

**Q: Tell me about a challenging bug you fixed.** A: AuraOS SQLite write-contention (see above) — good STAR structure: Situation (5 MCP servers, shared SQLite), Task (eliminate "database locked" errors), Action (redesign to single-writer), Result (stable concurrent writes, understood the durability/throughput tradeoff explicitly).

**Q: Tell me about a time you mentored or helped someone.** A: At Bitlance, continued informally mentoring and onboarding incoming interns after your own internship ended — walked them through project workflows and the codebase, received strong informal appreciation from the reporting coordinator (note: informal, no formal recognition process existed there — say this plainly if asked, don't oversell it as a formal title).

**Q: Tell me about the AI voice agent you built at Bitlance.** A: Led development of a production-grade AI voice agent using GPT-4, Whisper, and Google TTS, integrated with Twilio WebSockets and an Express.js backend, to automate outbound sales calls with real-time, natural-sounding streaming conversation. Also built a sentiment-aware CRM routing assistant (n8n + OpenAI) and an automated blog pipeline (GNews, GPT-4, DALL·E, WordPress REST API), all Dockerized across GCP/Twilio/CMS.

**Q: Describe a time you had to learn something fast / worked solo on something ambiguous.** A: All three portfolio projects (AuraOS, CollabEditor, Vaultik) were solo builds — pick whichever had the least prior familiarity going in (likely AuraOS's MCP architecture, since MCP was new/emerging) and narrate the learning curve honestly.

**Q: What's a weakness / something you'd do differently?** A: Good honest answer: CollabEditor's conflict resolution is last-write-wins rather than a proper CRDT — you know the "right" solution (Yjs/Automerge) but scoped it down for time; frame as a conscious tradeoff you'd revisit, not a gap you didn't notice.

---

## 6. Why Bain / Why BCN Labs

**Q: Why BCN Labs specifically?** A: It's not classic case consulting — it's a hands-on technical build team (GenAI/agentic + full-stack) embedded inside a consulting firm, working across Practices/CoEs/Clients on real production systems (RAG, MCP, agentic workflows, cloud-native apps). That's a closer match to what you actually enjoy building — AuraOS is essentially a personal-scale version of "agentic intelligence," and CollabEditor is full-stack delivery — than pure strategy work would be.

**Q: Why consulting/Bain over a pure tech company?** A: The interdisciplinary breadth — CS + Statistics + Economics + Operations Research applied across sectors (Retail, Pricing, Corporate Finance, Sustainability) — means building things that get applied to real, varied business problems quickly, rather than one product for years. Fine to say you're drawn to that variety and the R&D-meets-delivery model specifically, not "consulting culture" in the abstract.

**Q: Do you have questions for us?** A: Have 2-3 ready: e.g., "What does the MCP/tool-integration stack look like in a live BCN Labs engagement — is it closer to what I built solo in AuraOS, or a different framework internally?" or "How does BCN Labs balance research-grade experimentation vs. shipping production systems on client timelines?"

---

## Quick reminders before tomorrow

- Don't overclaim: CollabEditor has no CRDT/OT, no sandboxed execution, no Redis in the final build.
- Bitlance mentoring was informal — say so if asked.
- Lead with AuraOS for anything GenAI/MCP/agentic; lead with CollabEditor for anything full-stack; lead with Vaultik for anything concurrency/systems.
- If you don't know something, reason out loud rather than guessing silently — R&D-style interviews usually reward visible thinking process over a perfect final answer.