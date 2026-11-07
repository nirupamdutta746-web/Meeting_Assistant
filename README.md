# AI Meeting Assistant (n8n + Telegram + Gemini + Google Sheets)

An automated meeting assistant built with n8n that processes meeting transcripts via Telegram, extracts key action items and summaries using Google Gemini, and logs the structured data directly into Google Sheets. 

This workflow features dual-input routing, allowing users to either paste text directly or upload document files to the Telegram bot.

## 🚀 Features

* **Multi-Format Input:** Processes both standard Telegram text messages and document uploads (e.g., `.txt` files) using a Switch node for conditional routing.
* **AI Extraction:** Utilizes Google Gemini to parse messy transcripts and output clean, structured JSON data containing a meeting summary, task descriptions, assignees, and deadlines.
* **Automated Data Entry:** Iterates through extracted action items and logs each task as a separate row in Google Sheets.
* **Instant Feedback:** Sends a formatted summary message back to the Telegram chat once processing and logging are complete.
* **Loop Prevention:** Configured with specific "Execute Once" settings to ensure the user receives only one final notification, preventing notification spam.

## 📋 Prerequisites

To run this project locally, you will need:
* [Docker](https://www.docker.com/) and Docker Compose
* [ngrok](https://ngrok.com/) (to expose your local n8n instance to Telegram's webhooks)
* A Telegram Bot Token (via BotFather)
* Google Sheets API Credentials (Service Account)
* A Google Gemini API Key

## 🛠️ Setup & Installation

### 1. Start n8n
Clone this repository and start the n8n container using the provided configuration:
```bash
git clone [https://github.com/YOUR-USERNAME/YOUR-REPO-NAME.git](https://github.com/YOUR-USERNAME/YOUR-REPO-NAME.git)

cd n8n-meeting-assistant
docker-compose up -d
```

## 2. Configure Webhooks (Local Testing)
Start ngrok to expose port 5678:

```Bash
ngrok http 5678
```

Note: Copy the https:// forwarding URL provided by ngrok. Update the WEBHOOK_URL in the docker-compose.yml file with this URL and restart the container if you want it permanently set as an environment variable.

## 3. Import the Workflow
Navigate to your local n8n instance at http://localhost:5678 (or your ngrok URL).

Create a new workflow.

Click the menu in the top right corner and select Import from File.

Select the workflow.json file from this repository.

## 4. Connect Credentials
You will need to create and assign credentials within n8n for the following nodes:

Telegram Trigger / Telegram Node: Add your Telegram Bot Token.

Basic LLM Chain (Gemini): Add your Google Gemini API Key.

Google Sheets Node: Connect your Google Service Account credentials and select your target spreadsheet. (Ensure your sheet has columns for Meeting Date, Task Description, Assignee, Deadline, and Status).

5. Activate
Once all credentials are valid and the node mappings match your specific Google Sheet, toggle the workflow to Active in the top right corner of the n8n canvas.

## 💡 Usage
Open your configured Bot in Telegram.

Send a meeting transcript. You can either:

Paste the text directly into the chat.

Upload a document containing the transcript.

The bot will process the input, log individual action items into your Google Sheet, and reply with a clean summary of the meeting.

## 🏗️ Architecture Notes
Routing Logic: The workflow utilizes a Switch node evaluating {{ $json.message.text }} and {{ $json.message.document }} to handle standard text and file uploads dynamically.

Data Transformation: A Code node runs custom JavaScript to restructure the LLM's JSON output, ensuring perfect case-sensitive mapping for the Google Sheets appending process.
