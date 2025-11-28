#  RAG-AGENT | Chatbot 
##AI-Powered CRM Assistant & LinkedIn Automation Workflow

An advanced **n8n workflow** that turns SMS/WhatsApp (via Twilio) into a natural language interface for your CRM (**Attio**) and professional network (**LinkedIn**). 

This agent handles natural language queries to retrieve contact details, adds new contacts via image recognition (business cards), and automates LinkedIn interactions using Browserflow.

## 🚀 Key Features

* **Natural Language CRM Querying:** Text questions like *"Where does John Doe work?"* or *"When did I last speak to Jane?"* to receive real-time data from Attio.
* **Image-to-Contact (OCR):** Send a picture of a business card or contact details; the agent uses **GPT-4o** to parse the image and automatically create a new record in Attio.
* **Smart Context Routing:** An AI router determines if the user is asking about a person, a company, or social intelligence.
* **LinkedIn Automation (Browserflow):**
    * Check connection status.
    * Scrape recent posts for a contact.
    * Send connection invites automatically if not connected.
* **Conflict Handling:** Checks for existing emails in the CRM to prevent duplicate entries.

## 🛠️ Tech Stack & Integrations

* **[n8n](https://n8n.io/):** Workflow orchestration.
* **[Twilio](https://www.twilio.com/):** Inbound/Outbound SMS and MMS handling.
* **[OpenAI API](https://openai.com/):**
    * `gpt-4-turbo`/`gpt-4.1`: For logic routing, JSON filter generation, and response formatting.
    * `gpt-4o`: For image analysis (Vision).
* **[Attio CRM](https://attio.com/):** The database for People and Companies.
* **[Browserflow](https://browserflow.io/):** For headless browser automation on LinkedIn.

## ⚙️ Prerequisites

Before importing this workflow, ensure you have the following:

1.  **n8n Instance:** Self-hosted or Cloud version.
2.  **Twilio Account:** A phone number configured with a Webhook pointing to this n8n workflow.
3.  **Attio API Key:** With read/write permissions for People and Companies objects.
4.  **OpenAI API Key:** With access to GPT-4 models.
5.  **Browserflow Account:** With the n8n integration configured.

## 📥 Installation

1.  **Clone/Download:** Download the `workflow.json` file from this repository.
2.  **Import to n8n:**
    * Open your n8n dashboard.
    * Click **"Add Workflow"** > **"Import from..."** > **"File"**.
    * Select `workflow.json`.
3.  **Configure Credentials:**
    You will need to update the credential nodes within n8n or create new ones for:
    * `Twilio API`
    * `OpenAI API`
    * `Browserflow API`
4.  **Update API Headers:**
    * Locate the **HTTP Request** nodes (Attio calls).
    * Replace `Bearer [YOUR_ATTIO_API_KEY]` with your actual Attio Bearer token or map it from n8n credentials.

## 🤖 Usage Guide

Once the workflow is active and connected to your Twilio number, you can interact with it via SMS:

### 1. General CRM Queries
* **Input:** *"Who is John Smith?"*
* **Action:** Fetches person details and company association from Attio.
* **Response:** Returns a summarized text of the contact.

### 2. Company Lookups
* **Input:** *"In which company does Hassan work?"*
* **Action:** Triggers the company-specific filter logic to return the `associated_company`.

### 3. LinkedIn Intelligence
* **Input:** *"What has John Doe been posting about on LinkedIn?"*
* **Action:** Uses Browserflow to scrape the profile's recent activity.
* **Response:** Summarizes the last 5 posts.

### 4. Adding Contacts (Image)
* **Input:** *[Upload an image of a business card]*
* **Action:**
    1.  GPT-4o analyzes the image.
    2.  Extracts Name, Email, Phone, Job Title, etc.
    3.  Formats data into JSON.
    4.  Checks Attio for duplicates.
    5.  Creates the contact if unique.
* **Response:** "✅ Data Saved Successfully" with a summary of extracted fields.

## 🧩 Workflow Architecture

The workflow consists of two main branches triggered by **Twilio**:

1.  **Text Branch:**
    * **LLM Router:** Classifies the intent (CRM Search vs. Social Search).
    * **Filter Generator:** Converts natural language into Attio API JSON filters.
    * **Switch Logic:** Routes to specific API calls (Person query, Company query, or Browserflow automation).
    * **Formatter:** Uses an LLM to beautify the JSON response into a human-readable SMS.

2.  **Image Branch:**
    * **Media Extraction:** Downloads the image from Twilio.
    * **Vision Analysis:** Sends image to OpenAI to extract text fields.
    * **Validation:** Checks if an email exists (mandatory field).
    * **Upload:** POSTs the data to Attio.

## ⚠️ Important Notes

* **Browserflow & LinkedIn:** Automating LinkedIn actions (scraping/invites) carries a risk of account restriction. Use the Browserflow nodes with caution and ensuring you comply with platform Terms of Service.
* **Attio Object IDs:** The workflow uses specific Attio Object IDs (e.g., `company`, `job_title`). Ensure your Attio workspace schema matches these keys, or update the "Allowed Field IDs" prompt in the **Basic LLM Chain** node.

## 📄 License

This project is open-source. Please feel free to modify and adapt it to your specific CRM needs.
