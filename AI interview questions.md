**what is the difference between an LLM and an AI agent?**
Large language model(chatgpt) doesn't have a lot of context, used for general purposes like drafting an email
- input text
- tokenization
- neural processing
- prediction 
- output
Agent has tools, goal, memory and performs a specific task
Agent - flight booking agent
Multi agent - finds the best deals, applies voucher, books your ticket, sends an email

**what is genAI?**
AI that is capable of generating new content rather than just analyzing or classifying existing data.
- traditional AI - is this a spam email
- genAI - generate a new email 
GenAi can generate - text, images, audio, code
chatGPT is a genAI powered by LLMs
`RAG is a technique that gives a GenAI model relevant information before generating answer.`

**what architecture is used by most LLMS
- transformer based language models
**what is AI hallucination
- AI generating information that is incorrect/unsupported
**what is fine tuning?
- training a pretrained model further on specific dataset/task
- RAG provides external/contextual information; fine-tuning changes model behavior through additional training
**what does temperature control do in an LLM?
- model's randomness/variation in generation
- low temp - more predictable
- high temp - more varied/random

**what is enterprise search?**
Its like a google for a company's private information
`what is our company's parental leave policy?`
- Traditional Enterprise search : Employee-> Search: "parental leave policy" -> Search engine  
	-> Internal documents -> Relevant Documents (generally returns documents or links)
- GenAI Enterprise search : Employee -> "what is our parental leave policy?" -> RAG system(retrieves relevant docs) ->LLM -> output(employees are entitled to 6 month leaves)
- **Connectors → indexing → permissions → retrieval → LLM → answer**
- The system must not retrieve or expose documents that Employee B isn’t authorized to access.
**Prompt Engineering/Context Engineering/Knowledge Engineering**
- Prompt Engineering : how do i phrase my instruction
- Context Engineering : designing what information you give an AI model at the time it generates an answer.
- Knowledge Engineering : 

