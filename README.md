# Social Media Content Scheduler — n8n Workflow

![n8n](https://img.shields.io/badge/n8n-workflow-orange?logo=n8n)
![Google Sheets](https://img.shields.io/badge/Google-Sheets-green?logo=googlesheets)
![LinkedIn](https://img.shields.io/badge/LinkedIn-API-0077B5?logo=linkedin)
![Twitter/X](https://img.shields.io/badge/Twitter%2FX-API-black?logo=x)
![Gmail](https://img.shields.io/badge/Gmail-notifications-red?logo=gmail)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

> Manage your entire content calendar in Google Sheets — n8n auto-posts to LinkedIn and Twitter/X every morning, marks posts as done, and sends you a confirmation. Social media on autopilot.

---

## The Problem

Small businesses and creators spend hours every week:
- Manually logging into each platform to post
- Copying content from spreadsheets into posting interfaces
- Tracking what's been posted vs. what's pending
- Forgetting to post at optimal times

Social media consistency is critical for growth — but manual posting is a time sink.

## The Solution

Maintain a simple Google Sheets content calendar. Every morning at 8 AM, this workflow checks for due posts, publishes them to LinkedIn and/or Twitter/X, updates the sheet, and sends a confirmation email. **Zero manual posting required.**

---

## Architecture

```
Every Day at 8:00 AM
          │
          ▼
[Schedule Trigger]
          │
          ▼
[Google Sheets: Read
 Content Calendar]
          │
          ▼
[IF: Scheduled Date = Today
 AND Status = Pending?]
     │           │
    YES          NO
     │           │
     ▼           ▼
[IF: LinkedIn    [No-Op: Skip]
 or Twitter?]
   │        │
   ▼        ▼
[LinkedIn] [Twitter/X]
   │        │
   └────┬───┘
        ▼
[Sheets: Update Status = Posted]
        │
        ▼
[Gmail: Confirmation Email]
```

### Nodes

| Node | Purpose |
|------|---------|
| **Schedule Trigger** | Runs every day at 8:00 AM |
| **Google Sheets (read)** | Fetches entire content calendar |
| **IF (date check)** | Filters posts where date = today AND status = Pending |
| **IF (platform)** | Routes to LinkedIn or Twitter/X based on platform column |
| **HTTP → LinkedIn** | Posts via LinkedIn UGC API |
| **HTTP → Twitter/X** | Posts via Twitter API v2 |
| **Google Sheets (update)** | Sets Status = Posted and records timestamp |
| **Gmail** | Sends confirmation with post preview |

---

## Google Sheets Content Calendar Format

Create a sheet named **Content Calendar** with these columns:

| Column | Description | Example |
|--------|-------------|---------|
| **ID** | Unique row identifier | `POST-001` |
| **Scheduled Date** | Format: `YYYY-MM-DD` | `2025-01-20` |
| **Platform** | `LinkedIn` or `Twitter` | `LinkedIn` |
| **Content** | Post text | `Excited to share...` |
| **Image URL** | Optional image link | `https://...` |
| **Status** | `Pending` → `Posted` | `Pending` |
| **Posted At** | Auto-filled by workflow | _(auto)_ |

### Sample Content Calendar

| ID | Scheduled Date | Platform | Content | Status |
|----|---------------|----------|---------|--------|
| POST-001 | 2025-01-20 | LinkedIn | We just launched our new AI workflow... | Pending |
| POST-002 | 2025-01-20 | Twitter | 🚀 Big news: our n8n automation is live! | Pending |
| POST-003 | 2025-01-21 | LinkedIn | 5 automation mistakes most businesses make... | Pending |

---

## Setup Guide

### Prerequisites

- n8n instance (self-hosted or n8n.cloud)
- Google account with Sheets and Gmail
- LinkedIn Developer App with `w_member_social` permission
- Twitter Developer App with Read & Write permissions

### Step 1 — Import the workflow

n8n → **Workflows** → **Import from File** → select `workflow.json`

### Step 2 — Set up credentials

**Google Sheets OAuth2**
- Create in n8n Settings → Credentials
- Connect your Google account

**Gmail OAuth2**
- Same Google account connection

**LinkedIn API (HTTP Header Auth)**
- Name: `LinkedIn API`
- Header: `Authorization`
- Value: `Bearer YOUR_LINKEDIN_ACCESS_TOKEN`

> Get your token at [LinkedIn Developer Portal](https://developer.linkedin.com) — create an app, request `w_member_social` scope

**Twitter API (HTTP Header Auth)**
- Name: `Twitter API Bearer`
- Header: `Authorization`
- Value: `Bearer YOUR_TWITTER_BEARER_TOKEN`

> Get your keys at [developer.twitter.com](https://developer.twitter.com)

### Step 3 — Configure the workflow

1. **Google Sheets node** — replace `YOUR_GOOGLE_SHEET_ID` with your spreadsheet ID
2. **LinkedIn node** — replace `YOUR_LINKEDIN_PERSON_ID` with your LinkedIn profile URN
3. Set `NOTIFICATION_EMAIL` environment variable to your email address

### Step 4 — Set the schedule

The workflow runs daily at **8:00 AM** in your n8n server timezone. Adjust in the Schedule node if needed.

### Step 5 — Activate

Click **Activate** in n8n. The workflow will run automatically every morning.

---

## Testing

To test manually without waiting for 8 AM:
1. Click the **Execute Workflow** button in n8n
2. Or create a test row with today's date and Status = Pending

---

## Customization Ideas

- **Multi-platform** — add more IF branches for Facebook, Instagram (via Meta API)
- **AI-enhanced posts** — add a Groq node to rephrase content for each platform's style
- **Image posting** — extend the LinkedIn/Twitter nodes to attach images from the URL column
- **Approval gate** — add a webhook step that requires manual approval before posting
- **Buffer/Hootsuite** — replace the API nodes with Buffer's n8n integration

---

## Tech Stack

- **n8n** — scheduling and workflow engine
- **Google Sheets** — content calendar (no software to install, team-friendly)
- **LinkedIn UGC API** — professional content posting
- **Twitter API v2** — social media posting
- **Gmail** — daily confirmation digest

---

## Why Google Sheets as the Calendar?

- Your whole team can add/edit content without n8n access
- Built-in version history
- Easy to share with clients for review and approval
- No extra tools — you probably already use it

---

## Author

Built by **Dilovar Sam** — AI automation specialist.

[GitHub](https://github.com/dsam-ai) · [Portfolio](https://github.com/dsam-ai?tab=repositories)

---

## License

MIT — use it, extend it, ship it.
