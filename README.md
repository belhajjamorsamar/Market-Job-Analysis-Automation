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

# Nodes & Components

| Node Name | Type | Description |
| :--- | :--- | :--- |
| **When clicking ‘Execute workflow’** | `Manual Trigger` | Initiates the workflow on demand for testing and batch processing. |
| **HTTP Request** | `HTTP Request` | Triggers the Apify Actor (`linkedin-jobs-scraper`) synchronously with preset search parameters (e.g., `"data science"`, date filters). |
| **Split Out** | `Split Out` | Flattens the dataset array into individual job listing items. |
| **Loop Over Items** | `SplitInBatches` | Controls iteration flow to process jobs sequentially and avoid API rate limits. |
| **AI Agent & OpenRouter Model** | LangChain Agent + `meta-llama/llama-3.1-8b-instruct` | Parses raw job description texts and extracts specific metrics into strict JSON keys. |
| **Edit Fields** | `Set (Code/Expression)` | Cleans markdown tags from the AI output (`JSON.parse`) and merges the extracted details with original metadata (`Job ID`, `Job Link`, `Date Posted`). |
| **Append row in sheet** | `Google Sheets` | Maps and logs the finalized records directly into your designated Google Sheet (`Job_Tracking_Template`). |

---

## Extracted Data Schema

The AI extraction step transforms messy job descriptions into a clean, standardized schema mapped directly to your Google Sheets tracker:

* **Job Title**: The official title of the role.
* **Company**: The hiring organization.
* **Location**: City, country, or remote status.
* **Contract Type**: Full-time, part-time, contract, internship, etc.
* **Experience Level**: Entry-level, mid, senior, lead, etc.
* **Required Skills**: Key frameworks, programming languages, and technologies mentioned.
* **Salary / Compensation**: Stated salary ranges or compensation packages (if provided).
* **Core Responsibilities Summary**: A concise breakdown of the role's primary duties.
* **Job ID & Link**: Unique LinkedIn identifier and direct URL to apply.
* **Date Posted**: Timestamp of when the listing went live.

---

## Setup & Configuration

1. **Import the Workflow**: Copy the JSON workflow configuration and import it directly into your n8n instance.
2. **Credentials Required**:
   * **Apify API Token**: Configured inside the HTTP Request URL.
   * **OpenRouter Account**: Linked to the OpenRouter Chat Model node.
   * **Google Service Account**: Configured for the Google Sheets node to grant read/write access to your tracking spreadsheet.
3. **Customize Search Parameters**: Open the **HTTP Request** node and modify the `jsonBody` payload (such as keywords, `geoId`, or `f_TPR` time filters) to match your specific job search criteria.
