## 🔐 Credentials & Security

This repository does not include any API keys or credentials.

To run the workflow, users must configure their own credentials in n8n for:
- Google Sheets
- Google Drive
- LLM provider (Groq / OpenAI / etc.)

Given below, are the required environment variables.

# n8n Configuration
N8N_HOST=localhost
N8N_PORT=5678

# Google Credentials
GOOGLE_SHEETS_CREDENTIAL_ID=<your_google_sheets_credential_id>
GOOGLE_DRIVE_CREDENTIAL_ID=<your_google_drive_credential_id>

# LLM Provider
LLM_PROVIDER=groq
GROQ_API_KEY=<your_api_key_here>

# General
TIMEZONE=Asia/Kolkata

