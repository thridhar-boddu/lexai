# LexAI Colab — How To Use

## Before You Start

You need two things:
1. A Google account (for Colab)
2. A free Groq API key

**Get your free Groq key:**
1. Go to [console.groq.com](https://console.groq.com)
2. Sign up with Google or email — no credit card needed
3. Click **API Keys** in the left sidebar
4. Click **Create API Key**
5. Copy the key and save it somewhere — you will need it in Step 3 below

---

## Step 1 — Open The Notebook

1. Go to [colab.research.google.com](https://colab.research.google.com)
2. Click **File → Upload notebook**
3. Select the `LexAI_Colab.ipynb` file
4. The notebook opens with 4 cells

---

## Step 2 — Run Cell 1 (Install Packages)

Click the **▶ play button** on Cell 1 or press `Shift + Enter`.

Wait for it to finish. You will see:
```
✅ Dependencies installed
```

This only needs to run once per session.

---

## Step 3 — Run Cell 2 (Enter API Key)

Click **▶** on Cell 2.

A password-style input box appears:
```
Paste your Groq API key (from console.groq.com):
```

Paste your Groq key and press Enter. You will see:
```
✅ Groq API key set
```

> ⚠ If your Colab session disconnects and restarts, you must re-run Cell 2 to set the key again. It does not persist between sessions.

---

## Step 4 — Run Cell 3 (Start RAG Engine)

Click **▶** on Cell 3.

This builds the entire RAG engine in memory. You will see:
```
✅ RAG engine ready — powered by Groq + Llama 3.1
   {'total_chunks': 8, 'documents': 1, 'unique_terms': 43}
```

The 8 chunks shown are the built-in clause database that gets pre-loaded. Your contract has not been added yet.

---

## Step 5 — Run Cell 4 (Launch UI)

Click **▶** on Cell 4.

The full interface appears below the cell with:
- A file upload button
- A text paste area
- Analysis buttons
- An ask box

---

## Step 6 — Load Your Contract

**Option A — Upload a file:**
1. Click **📄 Upload PDF/TXT**
2. Select your PDF or TXT contract file
3. Click **Index Document →**

**Option B — Paste text:**
1. Copy your contract text
2. Paste it into the text area
3. Click **Index Document →**

You will see a confirmation message like:
```
✅ employment_agreement.txt — 17 clauses indexed (id: a3f9bc1d2e)
```

The number of clauses tells you how many chunks were created. A typical employment contract produces 12–20 chunks.

---

## Step 7 — Analyze The Contract

Click any of the four analysis buttons. Wait 5–15 seconds for the result to appear below.

---

### 📝 Summary Button
Gives you a plain-English overview of the contract structured as:
- What the contract is
- What each party must do
- Key amounts and deadlines
- How the contract ends

**Best for:** First read of any contract to understand what you are dealing with.

---

### ⚠ Risks Button
Scans the contract for dangerous clauses and returns color-coded results:

- 🔴 **HIGH** — Clauses that significantly limit your rights or expose you to large financial penalties
- 🟡 **MEDIUM** — Clauses that are unfavorable but common
- 🟢 **LOW** — Standard clauses worth noting but not alarming

Each risk card shows:
- The clause name
- A quote of the actual contract language
- A plain-English explanation of what it means
- A suggestion of what to negotiate or ask about

**Best for:** Quickly spotting the most dangerous parts of a contract.

---

### 📋 Key Terms Button
Extracts and displays all important factual information in a structured table:

| Field | Example |
|---|---|
| Contract Type | Employment Agreement |
| Party 1 | NovaTech Solutions LLC |
| Party 2 | Alex Johnson |
| Effective Date | March 1, 2025 |
| Payment Amount | $145,000/year |
| Governing Law | Delaware |
| Non-Compete | YES |
| Arbitration Required | YES |

**Best for:** Quickly verifying the key facts before reading the full contract.

---

### ❓ Questions Button
Generates 8–10 specific questions to ask a lawyer before signing. These are based on the actual clauses in your contract, not generic advice.

Example output for a contract with a worldwide non-compete:
```
1. The non-compete clause restricts me from working anywhere in the 
   world for 5 years — is this enforceable in my state?

2. Section 11 specifies $150,000 in liquidated damages — under what 
   circumstances would I actually owe this?
```

**Best for:** Preparing for a lawyer consultation and knowing what to focus on.

---

### Ask Box
Type any question about your contract and press **Ask →** or hit Enter.

**Good questions to ask:**

| Question | What it retrieves |
|---|---|
| What happens if I resign tomorrow? | Penalties, clawback, non-compete |
| Who owns my side projects? | IP assignment clause |
| Can I work for a competitor after leaving? | Non-compete scope and duration |
| What notice do I need to give? | Termination and auto-renewal clauses |
| What are all the ways I could owe money? | Liquidated damages, clawback, indemnification |

**The one question that tests everything:**
```
If I resign tomorrow, what are all the financial penalties, 
restrictions, and legal risks I face under this contract?
```

---

## Troubleshooting

**Rate limit error (429)**
You sent too many requests in one minute. Wait 60 seconds and try again. Groq's free tier allows 30 requests per minute.

**"GROQ_API_KEY not set" error**
Your session restarted and lost the key. Re-run Cell 2 and paste your key again.

**"Could not extract enough text" error**
Your PDF is a scanned image, not a text-based PDF. Copy and paste the text manually into the text area instead.

**UI buttons do nothing**
Cell 4 may have loaded before Cell 3 finished. Re-run Cell 3, wait for the confirmation message, then re-run Cell 4.

**Colab disconnected mid-analysis**
Run all 4 cells again from the top. You will need to re-index your document.

**Results seem wrong or incomplete**
Try the Ask box with a more specific question. The retrieval works on keyword overlap, so using words from the contract (like the section name) improves results.

---

## Tips For Best Results

- Use the contract's own terminology in the Ask box — if the contract says "liquidated damages", ask about "liquidated damages" not "penalty fee"
- Run **Key Terms** first to confirm the document indexed correctly and the AI found the right parties and dates
- Run **Risks** before **Questions** — the risk output helps you understand which questions matter most
- For long contracts (over 20 pages), the 40,000 character cap may cut off later sections — check that important clauses near the end were indexed by asking about them specifically

---

## Privacy Note

Your contract text is sent to Groq's servers for LLM processing. Do not upload contracts containing sensitive personal information (social security numbers, bank account details) unless you are comfortable with Groq's privacy policy at [groq.com/privacy](https://groq.com/privacy). The RAG indexing and retrieval happens entirely in your Colab session's local memory.
