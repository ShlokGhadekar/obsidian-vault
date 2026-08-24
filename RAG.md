**VECTOR DATABASE**
vector is list of binary values 
Vector database is a relational database around these vectors
Vector embedding: converting an audio/text/image file to a vector
Use ANN(approx nearest neighbour) to search the closest value
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



### The python code

```python
import math
from fastembed import TextEmbedding
from groq import Groq

# ============ SECTION 1: INGESTION / PREPROCESSING ============
def preprocess(text: str) -> str:
    return text.strip().lower()

def chunk_documents(docs: list[str], chunk_size=200, overlap=50) -> list[str]:
    chunks = []
    for doc in docs:
        doc = preprocess(doc)
        start = 0
        while start < len(doc):
            chunks.append(doc[start:start + chunk_size])
            start += chunk_size - overlap
    return chunks

# ============ SECTION 2: EMBEDDING ============
embed_model = TextEmbedding()

def embed_texts(texts: list[str]):
    return list(embed_model.embed(texts))

# ============ SECTION 3: IN-MEMORY VECTOR INDEX / RETRIEVAL ============
def cosine_similarity(vec1, vec2):
    dot_product = sum(a * b for a, b in zip(vec1, vec2))
    magnitude1 = math.sqrt(sum(a * a for a in vec1))
    magnitude2 = math.sqrt(sum(b * b for b in vec2))
    return dot_product / (magnitude1 * magnitude2)

def retrieve(query: str, chunks: list[str], chunk_vectors, top_k=2):
    query_vector = embed_texts([query])[0]
    scores = [cosine_similarity(query_vector, vec) for vec in chunk_vectors]
    ranked = sorted(zip(scores, chunks), reverse=True)
    return ranked[:top_k]

# ============ SECTION 4: GENERATION ============
client = Groq()

def generate_answer(query: str, retrieved_chunks) -> str:
    context = "\n".join(chunk for score, chunk in retrieved_chunks)
    prompt = f"""Answer using ONLY the context below. If the answer isn't there, say "I don't know."

Context:
{context}

Question: {query}
Answer:"""
    response = client.chat.completions.create(
        model="llama-3.3-70b-versatile",
        messages=[{"role": "user", "content": prompt}],
    )
    return response.choices[0].message.content

# ============ MAIN: TIE IT TOGETHER ============
if __name__ == "__main__":
    documents = [
        "the cat sat on the mat",
        "python is a programming language",
        "dogs are loyal animals",
    ]
    chunks = chunk_documents(documents)
    chunk_vectors = embed_texts(chunks)

    query = "what does a cat do"
    results = retrieve(query, chunks, chunk_vectors, top_k=2)
    answer = generate_answer(query, results)
    print(answer)
```

### 2. The pipeline, stage by stage

```
[Documents] → chunk → embed → store in vector DB     (offline, done once / on update)
                                     │
[User Query] → embed → similarity search ────────────► top-k relevant chunks
                                     │
                    prompt = instructions + chunks + query
                                     │
                              LLM generates answer
```

**a) Chunking** — split documents into small pieces (e.g. 300–500 tokens, with some overlap). Why not embed a whole document? Because embeddings compress meaning — a giant chunk blurs together many topics into one vector, hurting retrieval precision.

**b) Embedding** — convert each text chunk into a dense vector (e.g. 384 or 1536 numbers) using an embedding model (`all-MiniLM-L6-v2`, OpenAI `text-embedding-3-small`, etc). Semantically similar text → vectors that are close together (measured by cosine similarity).

**c) Vector store** — a database optimized for nearest-neighbor search over vectors (FAISS, Chroma, Pinecone, Weaviate). You store `(vector, original_text, metadata)`.

**d) Retrieval** — embed the user's query with the _same_ embedding model, then find the top-k closest chunks by similarity.

**e) Augmentation** — build a prompt like:

```
Answer the question using ONLY the context below. If the answer isn't in
the context, say you don't know.

Context:
{retrieved chunk 1}
{retrieved chunk 2}
...

Question: {user question}
```

**f) Generation** — send that prompt to the LLM, get the grounded answer back.

### 3. Terms interviewers love to probe

- **Chunk overlap** — why we overlap chunks (avoid splitting a sentence/idea across a hard boundary and losing it).
- **Top-k** — how many chunks to retrieve (too few → missing context; too many → noise, cost, dilutes attention).
- **Cosine similarity vs dot product vs Euclidean distance** — cosine is most common because it ignores magnitude, only measures direction/meaning.
- **Hallucination mitigation** — RAG doesn't eliminate hallucination, it reduces it by grounding; prompt instructions ("only use provided context") help further.
- **Re-ranking** — after initial vector retrieval (fast but approximate), a smaller cross-encoder model can re-score the top candidates for better precision.
- **RAG vs fine-tuning** — RAG for facts/knowledge that changes, fine-tuning for style/behavior/format.