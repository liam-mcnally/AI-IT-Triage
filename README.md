# AI IT Support Triage System

An automated, AI-driven IT support triage pipeline built with n8n. This system captures unstructured inbound support emails, uses Google's Gemini API to categorize and prioritize the requests, and conditionally routes them to Jira and Slack based on operational urgency.

## 📌 Project Overview
In a high-volume helpdesk environment, manually triaging inbound tickets creates bottlenecks and alert fatigue. This workflow automates the Level 1 dispatch process:
* **Reduces MTTA (Mean Time To Acknowledge):** Critical hardware or security failures instantly alert the on-call team in Slack.
* **Reduces Noise:** Standard software requests and low-priority inquiries silently generate Jira backlog tickets without pinging engineers.
* **Enforces Data Structure:** Converts unstructured email text into strict, actionable JSON payloads for clean ticketing.


## 🏗️ Architecture & Workflow

1. **Ingestion (IMAP):** Listens for incoming emails to a dedicated IT support inbox.
2. **AI Parsing (Gemini API):** Evaluates the email body and extracts structured JSON containing:
   * `summary`: A concise title for the ticket.
   * `issue_category`: Hardware, Software, Access, etc.
   * `priority_level`: Low, Medium, High, Critical.
3. **Conditional Routing (Switch Node):**
   * **Critical Path:** If priority is `Critical`, the workflow sends a formatted alert to the `#critical-it-alerts` Slack channel, then generates a Jira issue.
   * **Standard Path:** All other priorities bypass Slack and directly generate a structured Jira issue.
4. **System of Record (Jira Cloud):** Creates tasks with priority, category, and the original request text populated in the description.

## 🛠️ Tech Stack
* **n8n:** Workflow automation and orchestration (running locally via Docker).
* **Google Gemini:** LLM for natural language processing and JSON structuring.
* **Atlassian Jira Cloud API:** System of record for issue tracking.
* **Slack API:** Real-time incident alerting.

## 🚀 How to Use This Workflow

1. **Import the Workflow:**
   * Download `ITTriage.json` from this repository.
   * In your n8n workspace, go to the top right menu and select **Import from File**, or simply paste the JSON directly into your canvas.
2. **Configure Credentials:**
   * **IMAP:** Connect your dedicated support email address.
   * **HTTP Request:** Add your Gemini API key (via Google AI Studio) to the Header Auth.
   * **Jira SW Cloud API:** Add your Atlassian email, API Token, and Domain URL.
   * **Slack API:** Connect your Slack Bot OAuth token.
3. **Adjust Routing Logic (Optional):**
   * Open the **Switch** node to modify the routing rules (e.g., adding a specific path for 'Hardware' requests or different Slack channels).

## 🔒 Security Note
The included `ITTriage.json` file contains only the structural logic of the workflow. All API tokens, passwords, and workspace URLs are stripped automatically by n8n during export and are not exposed in this repository.
