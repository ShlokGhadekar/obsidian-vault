### 1. What problem RAG solves

An LLM only knows what was in its training data — frozen at some cutoff, no access to _your_ documents, your company's data, or anything private/recent. Two bad fixes:

- **Fine-tuning** — expensive, slow, and the model still can't cite sources or update live.
- **Stuffing everything into the prompt** — doesn't scale, context windows are limited and costly.

**RAG (Retrieval-Augmented Generation)** = at query time, _retrieve_ the small set of relevant documents from an external knowledge base, then _inject_ them into the prompt so the LLM generates its answer grounded in that retrieved text.

That's it conceptually: **Retrieve → Augment (the prompt) → Generate.**

**Pipeline:**
1. Chunk : cut long text into pieces
2. Embed : function that takes text in and gives out numbers. Similar text = similar numbers
3. Store and compare : store numbers in a list, compare the query's number against every stored number using similarity
4. Ask the LLM : paste the closest matching text into the prompt and ask your question

REDACTED_GROQ_KEY
