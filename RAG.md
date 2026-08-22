**Pipeline:**
1. Chunk : cut long text into pieces
2. Embed : function that takes text in and gives out numbers. Similar text = similar numbers
3. Store and compare : store numbers in a list, compare the query's number against every stored number using similarity
4. Ask the LLM : paste the closest matching text into the prompt and ask your question

