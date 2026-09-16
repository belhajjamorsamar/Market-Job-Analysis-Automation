# 🚀 LinkedIn Job Listings & AI Extraction Pipeline

An automated n8n workflow that scrapes live job postings from LinkedIn via Apify, processes the unstructured descriptions using an LLM via OpenRouter, structures the data, and logs everything directly into a Google Sheets tracker.

---

## **Workflow Architecture**

```text
[Manual Trigger] 
       │
       ▼
[HTTP Request (Apify Scraper)] 
       │
       ▼
[Split Out] ──► [Loop Over Items] ──► [AI Agent (OpenRouter / Llama 3.1)] 
                                              │
       ┌──────────────────────────────────────┘
       ▼
[Edit Fields (JSON Parsing)] ──► [Google Sheets (Append Row)] ──► [Loop Back]
