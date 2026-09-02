# Automated Audit Workpaper Summarizer

Real Benford's Law output from a run of this notebook, on the sample journal entries:

```
     observed_pct  expected_pct  deviation
1.0     21.60         30.10        8.50
2.0     25.31         17.61        7.70
3.0      0.00         12.49       12.49
5.0     32.72          7.92       24.80   <- biggest deviation
```

Digit "5" appearing as a leading digit **32.7% of the time** against an expected **7.9%** is a
textbook Benford's Law red flag — real financial data almost never clusters like that, which is
exactly the kind of pattern an auditor is trained to chase down.

## The pipeline

1. Load journal entries (date, account, debit/credit, description)
2. Flag round-dollar amounts, weekend postings, and duplicate amount/account pairs — classic manual
   journal entry red flags
3. Run the Benford's Law first-digit test across all entries
4. Score every entry by combining the flags into a single risk score
5. For the highest-risk entries, ask a free LLM to write the actual audit workpaper note explaining
   why it was selected and what to test next
6. Export a PDF workpaper ready for the audit file

## What the AI step adds

The statistics find the anomalies. The LLM step is what turns "risk_score = 5" into a properly
worded audit note an audit senior would actually write and file — in plain, professional language,
without a human retyping it from a spreadsheet.

## Confirmed working

The run hit Cerebras's free quota repeatedly (visible in the log — six separate `402` responses) and
fell back to Groq every single time, still producing workpaper notes for every flagged entry and
saving `audit_workpaper_summary.pdf` successfully.

## Stack

`pandas`/`numpy` for the statistics · `call_llm()` (Cerebras → Groq → Ollama) for the narrative ·
`reportlab` for the PDF

---
*Educational project. Not a substitute for professional audit judgment.*
