# LexAI 

An AI that reads your legal contracts and tells you what you're actually signing — built on genuine RAG so it answers from your document, not from guesswork.

---

## The Problem It Solves
Most people sign rental agreements, employment contracts, loan documents, and NDAs without truly understanding them. Legal language is deliberately complex. Hiring a lawyer for every contract is expensive. LexAI bridges that gap.

---

## What RAG Means and Why It Matters

**RAG = Retrieval Augmented Generation**

Without RAG, you paste a contract into ChatGPT and it answers from memory — it might hallucinate clauses, misquote figures, or miss things entirely.

With RAG, LexAI does this instead:

```
Your Contract
     ↓
Split into clauses        ← LegalDocumentChunker
     ↓
Index each clause         ← SparseVectorStore
     ↓
You ask a question
     ↓
Find the most relevant    ← Cosine similarity search
clauses from your doc
     ↓
Send ONLY those clauses   ← Augmented prompt
to the AI
     ↓
AI answers from your      ← Grounded answer
actual contract text
```

The AI only sees what was retrieved from your document. It cannot make things up because it is explicitly told to answer only from the retrieved clauses.

---

## The Four Cells Explained

### Cell 1 — Install Dependencies
Installs three packages:
- `httpx` — for making API calls
- `PyPDF2` — for reading PDF files
- `ipywidgets` — for the interactive UI buttons and text areas
- `nest_asyncio` — allows async code to run inside Colab's event loop

---

### Cell 2 — API Key
Sets your Groq API key as an environment variable. The key is entered through `getpass` so it is hidden.

Groq is free. It runs Llama 3.1, an open-source language model from Meta, on their servers at very high speed. No credit card required.

---

### Cell 3 — The RAG Engine

This is the core of the program. It contains five parts:

**Part 1: LegalDocumentChunker**
Splits your contract into individual clauses. It looks for legal heading patterns like `SECTION 1`, `ARTICLE 3`, `CLAUSE 7` and splits at those boundaries. This keeps each clause intact rather than cutting in the middle of a sentence. Each chunk is capped at 600 characters to keep memory usage low.

**Part 2: SparseVectorStore**
Indexes the chunks using TF-IDF — a mathematical technique that measures how important each word is in a clause relative to all other clauses. Rare legal terms like "indemnify" or "arbitration" get higher weight than common words. Stores everything as lightweight sparse dictionaries instead of large float arrays, which is why it does not crash Colab's free RAM.

**Part 3: LegalRAGPipeline**
The orchestrator. Handles ingesting documents, running retrieval queries, building the context string that gets sent to the LLM, and managing the built-in clause database.

**Part 4: Built-in Clause Database**
Eight pre-indexed risky clause patterns are loaded at startup:
- Non-Compete
- Mandatory Arbitration
- IP Assignment
- Auto-Renewal
- Liquidated Damages
- Indemnification
- At-Will Employment
- Salary Clawback

When you run Risk Analysis, your contract clauses are cross-referenced against this database so the AI knows what these patterns mean and how risky they are.

**Part 5: call_gemini function**
Despite the name, this now calls Groq's Llama 3.1 model. Sends the augmented prompt (retrieved chunks + your question) to the API and returns the response.

Do not relate this with gemini's api. This function was initially planned to use Gemini, but due to request limits, I was forced to find something much more affordable.

---

### Cell 4 — The UI

Builds the interactive interface using `ipywidgets`. Contains five analysis modes:

| Button | What it does | Sub-queries dispatched |
|---|---|---|
| 📝 Summary | Plain-English overview of the contract | 4 |
| ⚠ Risks | Color-coded risk analysis with explanations | 6 |
| 📋 Key Terms | Structured JSON of all important fields | 6 |
| ❓ Questions | List of questions to ask your lawyer | 4 |
| Ask → | Free-form question about the contract | 1 (top-6 chunks) |

Each button dispatches multiple sub-queries to the vector store, merges the results, deduplicates them, builds a context string from the top chunks, and sends it to the LLM with a specific instruction for that analysis type.

---

## How Retrieval Works

When you click **Risks**, the program runs these 6 queries against your document:

```
"termination penalty damages"
"non-compete restriction"
"indemnification hold harmless"
"arbitration waiver"
"intellectual property assignment"
"automatic renewal"
```

For each query it computes a cosine similarity score between the query and every indexed chunk. The highest scoring chunks are kept, duplicates removed, and the top 8 formatted into a context block like this:

```
[Clause 5.0 | Score: 0.4821]
Employee shall not engage in any competitive business
for 5 years worldwide after termination...

---

[Clause 11.0 | Score: 0.3940]
In the event of breach, Employee shall pay $150,000
as liquidated damages...
```

This context block — not the full contract — is what gets sent to the LLM.

---

## Memory Design

Colab free tier gives approximately 12GB RAM. Earlier versions crashed because they built dense float vectors (3,000 floats per chunk). The current version uses sparse dictionaries that store only non-zero terms.

```
50 chunks with dense vectors:   ~1.2 MB just for embeddings
50 chunks with sparse dicts:    ~0.01 MB
```

Documents are also capped at 40,000 characters on ingest. A typical employment contract is around 5,000–10,000 characters so most contracts use less than 25% of the cap.

---

## What The AI Can And Cannot Do

**Can do:**
- Explain what a specific clause means in plain English
- Flag clauses that are unusual or one-sided
- Tell you what financial penalties exist and under what conditions
- Extract specific figures like salary, notice periods, and penalty amounts
- Suggest targeted questions based on the actual content of your contract

**Cannot do:**
- Give legal advice — it will remind you of this
- Guarantee it found every risky clause — retrieval is not perfect
- Replace a qualified lawyer for important contracts
- Handle scanned image PDFs — needs text-based PDFs only
