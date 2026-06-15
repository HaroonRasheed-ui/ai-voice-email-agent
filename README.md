# 🎙️ voice-to-email-n8n

> Record your voice, describe who to email and what to say — the pipeline transcribes, extracts the recipient, drafts the message, and sends it via Gmail automatically.

---

## Overview

This repository contains a browser-based voice recorder (`recorder.html`) and an n8n workflow that together create a fully automated voice-to-email pipeline powered by Google Gemini and Gmail.

**You speak → AI listens → Email is sent.**

---

## How It Works

```
Browser mic recording
        ↓
  recorder.html  (binary audio upload)
        ↓
  n8n Webhook  (receives raw audio binary)
        ↓
  Gemini Flash  (transcribes audio to text)
        ↓  ← splits here
  AI Agent  (extracts & normalizes recipient email)
  Code  (combines email + transcript)
        ↓
  AI Agent  (generates message body)
        ↓
  Code  (extracts subject line)
  AI Agent  (formats HTML email body)
        ↓
  Gmail Node  (sends the email)
```

---

## Repository Structure

```
voice-to-email-n8n/
├── recorder.html          # Standalone browser recorder UI
├── workflow.json          # n8n workflow export (import this into n8n)
└── README.md
```

---

## Prerequisites

| Requirement | Notes |
|---|---|
| n8n instance | Self-hosted or n8n Cloud |
| Google Gemini API key | Via Google AI Studio — connect as `Google Gemini (PaLM) Api` credential in n8n |
| Gmail OAuth2 credential | Connected in n8n with send permission |
| HTTPS or localhost | Required for microphone access in the browser |

---

## Setup

### 1. Import the workflow

In your n8n instance:

1. Go to **Workflows → Import from file**
2. Select `workflow.json`
3. Open the workflow and attach your credentials:
   - All `Google Gemini Chat Model` nodes → your Gemini API credential
   - `Transcribe a recording` node → same Gemini credential
   - `Send a message` (Gmail) node → your Gmail OAuth2 credential

### 2. Activate the webhook

1. Open the **Webhook** node
2. Copy the **Production webhook URL** (looks like `https://your-n8n.com/webhook/f2265fc3-...`)
3. Activate the workflow using the toggle in the top right

### 3. Configure the recorder

Open `recorder.html` in a text editor and update the default webhook URL, or simply paste your webhook URL into the input field in the browser UI at runtime.

### 4. Use it

1. Open `recorder.html` in a browser (must be served over HTTPS or `localhost`)
2. Click **Record** and speak your message, e.g.:

   > *"Send an email to john.doe@gmail.com and tell him the meeting has been moved to Thursday at 3pm."*

3. Click **Stop**, then **Send**
4. The email arrives in the recipient's inbox within seconds

---

## Voice Command Tips

- **Always say the email address clearly**, including the domain: *"at gmail dot com"*, *"at outlook dot com"*
- You can spell it out if needed: *"j-o-h-n dot doe at gmail dot com"*
- Describe the content naturally — the AI will shape it into a proper email
- Works with greetings, reminders, cover letters, weather updates, and more

---

## Known Limitations & Improvement Notes

| Area | Current behaviour | Recommended fix |
|---|---|---|
| Email extraction | Separate AI Agent call with a merge node | Consolidate into a single Gemini call alongside transcription |
| Subject generation | Keyword/regex matching in Code3 | Delegate to Gemini for more natural subjects |
| HTML formatting chain | 3 sequential nodes (Code2 → Code5 → AI Agent2 → Code6) | Collapse into one prompted Gemini call |
| Error handling | No fallback paths anywhere | Add error outputs on all AI nodes and a notification fallback |
| Audio format | `audio/webm` or `audio/ogg` depending on browser | Safari produces different formats; test cross-browser |
| Recipient validation | Regex extraction only | Add a validation step before sending |

---

## Webhook Configuration

The webhook is configured to store the incoming audio under the binary property name `audio`. This matches the `binaryPropertyName: "audio"` setting in the Gemini transcription node.

If you change the webhook path or binary property name, update both the Webhook node and the `Transcribe a recording` node to match.

---

## Security Notes

- The webhook URL is publicly accessible — anyone with the URL can trigger the workflow and cause emails to be sent from your Gmail account
- Consider adding a secret header check in the Webhook node (`Header Auth`) for production use
- Store your `recorder.html` on a private or password-protected page if used internally

---

# AI Voice Email Agent

An AI-powered voice-controlled email automation system built with n8n, Google Gemini, Gmail API, and a custom web-based audio recorder.

This project allows users to record voice commands, automatically transcribe speech, extract recipient information, generate professional email content using AI, and send emails directly through Gmail.

---

# Features

* 🎤 Voice recording frontend
* 🧠 AI speech transcription with Gemini
* 📧 AI-generated professional emails
* 👤 Recipient name extraction
* 📒 Google Sheets contact lookup
* ✨ Automatic email subject generation
* 📨 Gmail integration
* 🌐 Webhook-based architecture
* 🎨 Responsive recorder frontend
* ⚡ Fully automated workflow using n8n

---

# Tech Stack

* n8n
* Google Gemini API
* Gmail API
* Google Sheets API
* HTML/CSS/JavaScript
* Webhooks
* AI Agents

---

# Workflow Overview

```text
Voice Input
   ↓
Audio Upload
   ↓
Gemini Transcription
   ↓
Recipient Extraction
   ↓
Google Sheets Contact Matching
   ↓
AI Email Generation
   ↓
Subject Generation
   ↓
HTML Formatting
   ↓
Gmail Delivery
```

---

# Project Structure

```text
├── Voice Assistant.json
├── Email Voice Assistant.json
├── recorder.html
└── README.md
```

---

# Installation

## 1. Clone the Repository

```bash
git clone https://github.com/yourusername/ai-voice-email-agent.git
cd ai-voice-email-agent
```

---

## 2. Import Workflow into n8n

* Open n8n
* Import:

  * `Voice Assistant.json`
  * OR `Email Voice Assistant.json`

---

## 3. Configure Credentials

Add the following credentials inside n8n:

* Google Gemini API
* Gmail OAuth2
* Google Sheets API

---

# Google Sheets Format

Example contact sheet:

| Name       | Email                                         |
| ---------- | --------------------------------------------- |
| John Doe   | [johndoe@gmail.com](mailto:johndoe@gmail.com) |
| Sarah Khan | [sarah@gmail.com](mailto:sarah@gmail.com)     |

---

# Configure Webhook

Update the webhook URL inside:

```text
recorder.html
```

Example:

```js
http://localhost:5678/webhook/your-webhook-id
```

---

# Usage

1. Open `recorder.html`
2. Click **Record**
3. Speak your email request
4. Click **Stop**
5. Click **Send**
6. The AI workflow will:

   * transcribe audio
   * identify recipient
   * generate email
   * send email automatically

---

# Example Commands

```text
Send an email to John saying I will join tomorrow's meeting at 10 AM.
```

```text
Email Sarah a reminder about the project deadline.
```

```text
Send a thank you email to Ahmed for the interview opportunity.
```

---

# Future Improvements

* Authentication & webhook security
* Rate limiting
* Multi-language support
* Voice reply support
* Conversation memory
* RAG-based contact search
* Docker deployment
* WhatsApp/Slack integrations
* Better error handling
* Email approval step before sending

---

# Security Notes

This project currently uses public webhook endpoints.

For production usage:

* Add authentication
* Enable request validation
* Add rate limiting
* Restrict file upload size
* Validate recipient emails

---

# Why This Project Matters

This project demonstrates:

* AI workflow orchestration
* LLM integration
* automation engineering
* real-world AI use cases
* API integrations
* frontend + backend coordination
* prompt engineering
* event-driven architectures

---

# License

MIT License

---

# Author

Built with ❤️ using n8n + Gemini AI
