### 1. MCP (you built this — just formalize the vocabulary)

**What it is:** Model Context Protocol — a standard interface so an LLM/agent can discover and call external tools ("servers") without every integration being custom-built. Client (the agent) ↔ Server (exposes tools/resources) ↔ Transport (usually stdio or HTTP).  
**Your proof:** AuraOS's 5-server architecture — GitHub, calendar, browser, memory, workflow engine — each an independent MCP server.  
**Interview soundbite:** "MCP standardizes tool discovery and invocation, so instead of hardcoding integrations, the agent core queries available tools and calls them uniformly — I used this directly, with 5 independent FastAPI MCP servers in AuraOS."

### 2. Agentic & multi-agent workflows (you built this too)

**What it is:** An agent plans multi-step actions, calls tools, observes results, and decides the next step — as opposed to one-shot LLM calls. Multi-agent = several specialized agents (planner, executor, reviewer) collaborating instead of one generalist.  
**Your proof:** AuraOS's workflow engine decomposes natural-language instructions into sequences of MCP tool calls.  
**Gap to know:** You built single-agent, not multi-agent. If asked directly, say so — then explain when multi-agent helps (separation of concerns, adversarial review) vs. hurts (coordination overhead for simple tasks).

### 3. RAG — Retrieval-Augmented Generation (partial overlap via memory system)

**What it is:** Ground LLM output in retrieved external documents at inference time, instead of baking facts into weights. Pipeline: chunk documents → embed → store in vector DB → embed the query → retrieve nearest chunks → stuff into prompt → generate.  
**Your proof:** AuraOS's ChromaDB tier does semantic vector search for memory recall — same retrieval mechanic as RAG, just applied to "past agent memory" instead of "a document corpus."  
**Interview soundbite:** "I haven't built RAG over a document corpus specifically, but I built the same retrieval mechanic — embedding-based semantic search — as one tier of AuraOS's memory system."  
**One gap fact to know:** RAG vs. fine-tuning — RAG for frequently-changing/traceable knowledge, fine-tuning for consistent behavior/format/style.

### 4. LLM tooling: fine-tuning & evals (no project overlap — pure basics)

**Fine-tuning, one line:** Further training a pretrained model on a smaller task-specific dataset to shift its behavior/style — expensive relative to RAG, used when retrieval can't give you the needed consistency.  
**Evals, one line:** Define success criteria and a held-out test set up front, then measure against it — exact match, similarity score, or LLM-as-judge. For agents specifically: tool-selection accuracy and task-completion rate, not just output text quality.  
**Interview soundbite:** "I'd approach evals by defining task success criteria before building, keeping a held-out test set, and for agentic systems specifically tracking tool-call accuracy, not just final output quality."

### 5. Machine Learning & Deep Learning (no project overlap — bare basics only)

Given zero time: know **bias-variance tradeoff** (underfit = too simple, misses patterns; overfit = memorizes noise, fails to generalize) and be able to name **regularization, cross-validation, more data** as fixes. That's the one question you're most likely to get cold on this line — don't need more than that tonight.

1.Linear Regression : predicting temp change, stock prices
draws a straight line between x and y(price of a house based on its size, as price increases)
### 6. Forecasting & Optimization (no project overlap — bare basics only)

One line each: **Forecasting** predicts future values from historical/time-series data (e.g., demand forecasting). **Optimization** finds the best solution under constraints (e.g., linear programming for resource allocation). If asked "have you done this," be honest — no — then pivot: "but this maps conceptually to what I did in Vaultik with throughput/latency tradeoffs under constraints."

### 7. Experimental Design & Automation (partial overlap via Bitlance)

**Your proof:** The n8n + OpenAI sentiment-routing assistant and the automated GNews→GPT-4→DALL·E→WordPress blog pipeline from Bitlance — both are automated pipelines with decision logic, which is the automation half of this line.