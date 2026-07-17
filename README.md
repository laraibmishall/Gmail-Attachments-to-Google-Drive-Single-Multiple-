# Gmail Attachment Auto-Upload to Google Drive

An automated [n8n](https://n8n.io/) workflow that monitors a Gmail inbox and automatically uploads all email attachments to Google Drive — supporting both single and multiple attachments per email, with zero manual intervention.

## 📋 Overview

This workflow eliminates the manual process of downloading email attachments and re-uploading them to cloud storage. It watches your Gmail inbox in real time, detects attachments (regardless of how many are in a single email), and uploads each one to Google Drive while preserving the original filename. A Discord notification confirms when the upload is complete.

## ⚙️ How It Works

```
Gmail Trigger → Filter → Split Out → Upload to Google Drive → Discord Notification
```

1. **Gmail Trigger**
   Watches the inbox in real time and fires whenever a new email arrives.

2. **Filter**
   Checks each incoming email for attachments using:
   ```
   {{ Object.keys($binary).length > 0 }}
   ```
   Emails with no attachments are automatically discarded.

3. **Split Out**
   Breaks emails containing multiple attachments into individual items (e.g., `attachment_0`, `attachment_1`, `attachment_2`...), so each file can be processed independently.

4. **Upload File (Google Drive)**
   Dynamically detects each item's binary field and filename instead of relying on a hardcoded key:
   ```
   Input Data Field Name: {{ Object.keys($binary)[0] }}
   File Name: {{ $binary[Object.keys($binary)[0]].fileName }}
   ```
   This allows the workflow to correctly upload files regardless of how many attachments were in the original email.

5. **Discord Notification**
   Sends a confirmation message to a Discord channel once the upload completes.

## ✨ Key Features

- **Handles any number of attachments** — works whether an email has 1 attachment or 10, thanks to dynamic binary field detection.
- **Smart filtering** — automatically skips emails with no attachments.
- **Preserves original filenames** — each file is uploaded to Drive with its original name and extension intact.
- **Real-time processing** — no polling delays or manual triggers required.
- **Notification support** — instant Discord alerts on successful upload.

## 🛠️ Tech Stack

| Component | Purpose |
|---|---|
| [n8n](https://n8n.io/) | Workflow automation engine |
| Gmail API | Email trigger and attachment retrieval |
| Google Drive API | File storage |
| Discord Webhook | Status notifications |

## 🔑 Prerequisites

- An active [n8n](https://n8n.io/) instance (cloud or self-hosted)
- A Google account with:
  - Gmail API access (OAuth2 credentials)
  - Google Drive API access (OAuth2 credentials)
- A Discord server with a webhook or bot integration configured

## 🚀 Setup Instructions

1. **Clone or download this repository.**

2. **Import the workflow into n8n:**
   - Open your n8n instance
   - Go to **Workflows → Import from File**
   - Select the `.json` workflow file from this repo

3. **Configure credentials:**
   - **Gmail Trigger / Get Message nodes** → connect your Gmail OAuth2 credential
   - Enable **"Download Attachments"** in the node's Options
   - **Upload File node** → connect your Google Drive OAuth2 credential and select the target folder
   - **Discord node** → add your Discord webhook URL or bot credential

4. **Activate the workflow** using the toggle in the top-right corner of the n8n editor.

5. **Test it:**
   - Send yourself an email with one attachment, then another with multiple attachments
   - Confirm both are uploaded correctly to the target Google Drive folder
   - Confirm the Discord notification is received

## 📂 Project Structure

```
├── workflow.json          # n8n workflow export
└── README.md               # Project documentation
```

## 🧩 Troubleshooting

| Issue | Cause | Fix |
|---|---|---|
| Filter discards emails with attachments | "Download Attachments" not enabled on Gmail node | Enable it in the node's Options |
| Upload node error: "item has no binary field" | Hardcoded binary field name (e.g., `attachment_0`) doesn't match the current item | Use `{{ Object.keys($binary)[0] }}` instead of a fixed field name |
| File Name shows `[undefined]` | File Name expression references a nonexistent binary key | Use `{{ $binary[Object.keys($binary)[0]].fileName }}` |

## 📄 License

This project is open-source and available for personal or commercial use. Feel free to fork and adapt it to your own workflow needs.

## 🙋 Use Case

Ideal for individuals or teams who regularly receive file attachments via email (resumes, invoices, reports, etc.) and want them automatically organized into a centralized Google Drive folder — without manually downloading and re-uploading each file.
