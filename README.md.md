# AI Invoice Processing Automation (n8n)

An automated workflow built with **n8n** to parse incoming PDF invoices, extract key financial metadata using AI, validate numbers, log entries to Google Sheets, and alert your finance team via Gmail.

---

## 🛠️ What This Workflow Does

1. **Monitors Google Drive:** Automatically picks up new PDF invoices uploaded to a specific folder.
2. **Extracts Text & AI Metadata:** Uses OpenAI (or Ollama/local LLMs) to extract key fields: `invoice_id`, `vendor`, `date`, `subtotal`, `tax`, and `total`.
3. **Validates Data:** Checks for missing fields and verifies invoice math (`subtotal + tax = total`).
4. **Appends to Google Sheets:** Saves valid invoices directly to your central ledger.
5. **Notifies Finance Team:** Sends an email via Gmail summarizing the invoice for approval (or an alert if validation fails).

---

## 📋 Prerequisites

Before importing the workflow, make sure you have:
* An active **n8n** instance (self-hosted or n8n Cloud).
* A **Google Workspace / Gmail Account** with API credentials setup in n8n for:
  * Google Drive (Read/Download access)
  * Google Sheets (Read/Write access)
  * Gmail (Send mail permissions)
* An **OpenAI API Key** (or an active Ollama instance if using self-hosted AI).

---

## 🚀 Quick Setup Instructions

### 1. Import the Workflow into n8n
1. Download the `ai_invoice_processing_n8n_workflow.json` file.
2. Open your n8n dashboard.
3. Click **Workflows** → **Import from File** and upload the JSON file.

### 2. Configure Credentials
Open each node and attach your credentials:
* **Google Drive Trigger & Download Nodes:** Select your Google Drive OAuth2 credentials.
* **AI Invoice Extractor Node:** Add your OpenAI API key (or switch the model provider to Ollama/Anthropic if needed).
* **Google Sheets Node:** Connect your Google Sheets account and enter your target **Spreadsheet ID**.
* **Gmail Nodes:** Select your Gmail account credentials for sending email notifications.

### 3. Setup Your Google Sheet Column Headers
In your Google Sheet (`Sheet1`), ensure Row 1 has the following exact column headers:

| invoice_id | vendor | date | subtotal | tax | total | status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |

---

## ⚙️ Customization & Validation Rules

The **Validate Extracted Data** Code node runs the following logic:
* Checks that required fields (`vendor`, `date`, `total`) are present.
* Ensures `abs((subtotal + tax) - total) <= 0.05` to account for minor rounding differences.

If validation fails, the workflow bypasses Google Sheets and sends an urgent exception alert directly to the finance team.

---

## 🧪 Testing

1. Drop a sample PDF invoice into your monitored Google Drive folder.
2. Click **Test Step** or **Execute Workflow** inside n8n to inspect execution data at each stage.
3. Turn **Active** ON in the top right corner of n8n to leave the automation running in the background.