

General-purpose checklist for any product/tech-company interview (not Razorpay-specific). Ordered by how likely each domain is to come up and how much return you get per hour of prep. Work top to bottom if time is limited.

---
## How to Use This Checklist Efficiently

1. **Time-box each domain.** Spend no more than 1–2 hours per Priority 1 domain, 30–45 min per Priority 2/3 domain. This is a refresher, not a first-time learning pass.
2. **Practice out loud, not just in your head.** For system design and AI/MCP questions especially, the ability to _explain_ clearly matters as much as knowing the answer.
3. **Anchor every abstract concept to one of your own projects** wherever possible (the doc above shows you where each concept maps to Retentia/CollabEditor/Vaultik/AuraOS) — this makes answers memorable and demonstrates applied understanding instead of textbook recall.
4. **Do Priority 1 and 2 even if the role sounds AI-focused** — nearly every technical interview still tests core CS fundamentals regardless of domain.
5. **Skip deep-diving into anything not in this list** unless a specific JD calls for it (e.g., Kubernetes, Kafka internals) — this list is deliberately scoped to what's most commonly asked, not exhaustive.
## PRIORITY 1 — Almost Always Asked (do these first)

### 1. Data Structures & Algorithms

The single highest-frequency category. If you only prep one thing, prep this.

**Must-know patterns (in order of frequency):**

1. Arrays + Two Pointers — e.g., "Two Sum," "Container With Most Water," "3Sum"
2. Hashmaps — "Group Anagrams," "Longest Substring Without Repeating Characters"
3. Sliding Window — "Maximum Subarray," "Minimum Window Substring"
4. Binary Search — "Search in Rotated Sorted Array," "Find First/Last Position"
5. Trees (BFS/DFS) — "Level Order Traversal," "Lowest Common Ancestor," "Diameter of Binary Tree"
6. Graphs — "Number of Islands," "Course Schedule" (topological sort), "Clone Graph"
7. Dynamic Programming (basic) — "Climbing Stairs," "House Robber," "Longest Common Subsequence," "Coin Change"
8. Linked Lists — "Reverse Linked List," "Detect Cycle," "Merge Two Sorted Lists"
9. Stacks/Queues — "Valid Parentheses," "Next Greater Element," "Min Stack"
10. Heaps — "Kth Largest Element," "Merge K Sorted Lists"

**Efficient approach:** Do 2–3 problems per pattern, not 20 random problems. Know the pattern well enough to derive it, not memorize the exact solution.

**Also know cold:**

- Big-O for every solution you write (time AND space) — you will be asked
- Why you chose one data structure over another

---

### 2. OOP Fundamentals

Nearly universal, especially for Java/C++ backgrounds like yours.

**Must-know questions:**

- Explain encapsulation, inheritance, polymorphism, abstraction — with a real example from one of your own projects (not a textbook animal example)
- Difference between abstract class and interface (and when to use which)
- Method overloading vs. overriding
- What is a constructor? Can a constructor be private? Why?
- Difference between `==` and `.equals()` in Java
- What is composition vs. inheritance — "favor composition over inheritance," be ready to explain why
- SOLID principles — at least know the acronym and give one example each (don't need deep mastery, just recognition)

---

### 3. Databases (DBMS + SQL)

**Must-know questions:**

- ACID properties — define each, give an example of a violation
- Normalization — 1NF/2NF/3NF, why we normalize, when we deliberately denormalize
- Indexing — how a B-tree index speeds up queries, cost of indexing on writes
- SQL: write a JOIN (inner/left/right), a GROUP BY with HAVING, a subquery
- Difference between SQL and NoSQL, when to use each
- Transactions and isolation levels (at least know the 4 levels exist: Read Uncommitted, Read Committed, Repeatable Read, Serializable)
- Primary key vs. foreign key vs. unique key vs. composite key

---

### 4. Operating Systems

**Must-know questions:**

- Process vs. Thread — differences, when to use which
- Deadlock — 4 necessary conditions, how to prevent/avoid
- Mutex vs. Semaphore
- Paging vs. Segmentation (basic definitions)
- What happens when you run a program (compilation → linking → loading → execution) — common opener question
- CPU scheduling algorithms — FCFS, Round Robin, Priority (just definitions, not deep math)
- What is a race condition? How do you prevent one? (You can use your Vaultik project here — real example of solving this.)

---

## PRIORITY 2 — Very Commonly Asked (do these next)

### 5. Computer Networks

**Must-know questions:**

- TCP vs. UDP — differences, when each is used
- What happens when you type a URL into a browser? (Very common opener — DNS resolution → TCP handshake → HTTP request → response → render)
- HTTP methods (GET, POST, PUT, DELETE, PATCH) and status codes (200, 201, 400, 401, 403, 404, 500)
- What is a REST API? What makes an API "RESTful"?
- Difference between HTTP and HTTPS, basic TLS handshake concept
- WebSocket vs. HTTP polling (you can speak to this directly from CollabEditor)

---

### 6. System Design (Low-Level + High-Level)

Expect at least a lightweight version of this even in fresher interviews.

**Most-asked system design questions (practice these specifically, in this order):**

1. **Design a URL Shortener** — classic warm-up; covers hashing, DB schema, scaling reads
2. **Design a Rate Limiter** — covers token bucket/sliding window algorithms, why it matters
3. **Design a Notification System** — covers queues, fan-out, retries
4. **Design a Parking Lot / Library Management System** — low-level OOP class design, not infra
5. **Design a Payment/Transaction System** (relevant broadly, not just fintech) — covers idempotency, retries, webhooks, consistency
6. **Design a Chat Application** — covers WebSockets, message ordering, delivery guarantees (you have direct experience here via CollabEditor)
7. **Design an E-commerce Cart/Checkout flow** — covers consistency vs. availability tradeoffs

**Core concepts to know cold before attempting any of the above:**

- Load balancing (round robin, least connections)
- Caching (write-through vs write-back, cache invalidation, when to use Redis-style caching)
- Horizontal vs. vertical scaling
- CAP theorem (Consistency, Availability, Partition tolerance — pick 2, know a real example of each tradeoff)
- Database sharding vs. replication
- Message queues (why async processing, at-least-once vs. exactly-once delivery)
- Idempotency (especially important — comes up in almost every payment/API design question)

**Efficient approach:** Don't try to master all of system design theory abstractly. Instead, practice explaining the 7 questions above out loud, drawing boxes-and-arrows, for 15–20 minutes each. Pattern recognition matters more than raw theory here.

---

## PRIORITY 3 — Relevant Given Your Profile (AI/Agents-specific)

Since your projects (AuraOS, Retentia) are AI-heavy, expect these if the interviewer engages with your resume at all.

### 7. AI Agents & LLM Concepts

**Must-know questions:**

- What is an "AI agent" vs. a plain LLM call? (Agent = LLM + tools + memory + a loop that decides actions)
- What is function calling / tool calling in LLMs? Walk through the request-response cycle.
- What is a context window, and what happens when you exceed it?
- Prompt vs. system prompt — what's the difference and why does it matter?
- What's the difference between fine-tuning and prompt engineering / RAG? When would you use each?
- What is RAG (Retrieval-Augmented Generation)? Walk through the pipeline: embed → store in vector DB → retrieve top-k → inject into prompt.
- Why would you use a small model for one task and a large model for another? (You can answer this directly from AuraOS — Groq's 8B for classification, 70B for planning — cost/latency tradeoff)

### 8. MCP (Model Context Protocol)

Since AuraOS uses this, be ready to explain it cleanly — it's a newer concept and interviewers may probe deeper if it's unfamiliar to them, or test you hard if it's familiar to them.

**Must-know questions:**

- What is MCP, in one sentence? (A standardized protocol/interface that lets LLM applications connect to external tools, data sources, and services in a consistent way, rather than each integration being custom-built.)
- Why does a standard protocol matter here? (Without it, every LLM app needs bespoke integration code per tool; MCP decouples the agent from the tool implementation.)
- What does an MCP "server" actually do? (Exposes a set of tools/resources over a defined interface that an MCP-compatible client/agent can discover and call.)
- In AuraOS, you ran 5 independent MCP servers — be ready to explain why you split responsibilities across multiple servers instead of one monolith (isolation of concerns, independent scaling/failure domains — and be honest that this is also what caused your SQLite multi-writer contention bug).
- How does an agent decide _which_ tool/MCP server to call? (Typically the LLM is given tool schemas/descriptions and selects based on the user's request — function-calling style reasoning.)

### 9. Vector Databases & Embeddings (used in AuraOS via ChromaDB)

**Must-know questions:**

- What is an embedding? (A dense numerical vector representation of text/data that captures semantic meaning, such that similar meanings are close in vector space.)
- How does semantic search differ from keyword search?
- What is cosine similarity and why is it used to compare embeddings?
- Why use a vector DB instead of just storing embeddings in a normal SQL table? (Optimized for high-dimensional nearest-neighbor search at scale — approximate nearest neighbor indexing like HNSW.)

---

## PRIORITY 4 — Good to Have, Lower Frequency

### 10. Concurrency & Multithreading (Java-specific, since Vaultik uses this heavily)

- `synchronized` vs `ReentrantLock` vs `ReentrantReadWriteLock`
- `volatile` keyword — what it guarantees (visibility, not atomicity)
- `ConcurrentHashMap` internals at a high level (segment/bucket-level locking)
- Producer-consumer problem — classic concurrency interview question
- What is a race condition vs. a deadlock vs. a livelock

### 11. Software Engineering Practices

- Git basics: merge vs. rebase, what a merge conflict is and how you resolve one
- CI/CD — what it is, why it matters (you can mention Docker/GCP deployment experience here)
- Unit testing vs. integration testing (you have unit tests in Retentia's src/ — mention this)
- Code review — what you look for when reviewing someone else's code

---

