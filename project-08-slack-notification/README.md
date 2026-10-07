# Project 08 — Contact Form → Slack Notification Pipeline

## Overview
An automated pipeline that connects a Tally contact form 
to Slack via n8n. The moment anyone submits an inquiry, 
a fully formatted Block Kit notification lands in the 
correct Slack channel within seconds — routing high-value 
inquiries to a dedicated #hot-leads channel and standard 
inquiries to #new-inquiries automatically.

## Tools Used
- n8n Cloud (automation engine)
- Tally (contact form builder)
- Slack (notification destination)
- Slack Block Kit (rich message formatting)

## The Problem This Solves
Contact form inquiries sitting unread in an email inbox 
for hours is one of the most common and costly problems 
small businesses face. This pipeline eliminates that 
entirely — every inquiry is delivered to the team 
instantly, formatted clearly, and ready to act on.

## Workflow Structure
```
[Tally Form Submission]
        ↓
[Webhook Trigger] — receives form data instantly
        ↓
[Edit Fields] — cleans data, adds timestamp + ref number
        ↓
[IF Node] — checks budget for urgency routing
    ↓                        ↓
[True — ₦1M+]           [False — Standard]
    ↓                        ↓
[Slack — #hot-leads]    [Slack — #new-inquiries]
🔥 urgent formatting    📬 standard formatting
```

## Form Fields Collected
- Full Name
- Email Address
- Phone Number
- Service Interest (dropdown)
- Budget Range (dropdown)
- Message

## How Tally Connects to n8n
1. Build form in Tally
2. Go to Integrate → Webhooks in Tally dashboard
3. Paste n8n Webhook node URL
4. Submit test entry
5. n8n receives payload instantly

## IF Node Configuration
```
Value 1:    {{ $json.budget }}
Operation:  Contains
Value 2:    ₦1M
```
Routes ₦1M-₦5M and Above ₦5M to #hot-leads.
Everything else goes to #new-inquiries.

## Slack Block Kit — Standard Message
```json
[
  {
    "type": "header",
    "text": {
      "type": "plain_text",
      "text": "📬 New Contact Form Inquiry"
    }
  },
  {
    "type": "section",
    "fields": [
      {
        "type": "mrkdwn",
        "text": "*Name:*\n{{ $json.name }}"
      },
      {
        "type": "mrkdwn",
        "text": "*Email:*\n{{ $json.email }}"
      }
    ]
  },
  {
    "type": "section",
    "fields": [
      {
        "type": "mrkdwn",
        "text": "*Service:*\n{{ $json.service }}"
      },
      {
        "type": "mrkdwn",
        "text": "*Budget:*\n{{ $json.budget }}"
      }
    ]
  },
  {
    "type": "section",
    "text": {
      "type": "mrkdwn",
      "text": "*Message:*\n{{ $json.message }}"
    }
  },
  {
    "type": "divider"
  },
  {
    "type": "context",
    "elements": [
      {
        "type": "mrkdwn",
        "text": "📅 {{ $json.received_at }} | 📱 {{ $json.phone }}"
      }
    ]
  }
]
```

## Slack Block Kit — HOT Lead Message
```json
[
  {
    "type": "header",
    "text": {
      "type": "plain_text",
      "text": "🔥 HIGH VALUE INQUIRY — Respond Immediately"
    }
  },
  {
    "type": "section",
    "fields": [
      {
        "type": "mrkdwn",
        "text": "*Name:*\n{{ $json.name }}"
      },
      {
        "type": "mrkdwn",
        "text": "*Email:*\n{{ $json.email }}"
      }
    ]
  },
  {
    "type": "section",
    "fields": [
      {
        "type": "mrkdwn",
        "text": "*Service:*\n{{ $json.service }}"
      },
      {
        "type": "mrkdwn",
        "text": "*Budget:*\n*{{ $json.budget }}* 💰"
      }
    ]
  },
  {
    "type": "section",
    "text": {
      "type": "mrkdwn",
      "text": "*Message:*\n{{ $json.message }}"
    }
  },
  {
    "type": "divider"
  },
  {
    "type": "section",
    "text": {
      "type": "mrkdwn",
      "text": "⚡ *Follow up within 30 minutes.*"
    }
  },
  {
    "type": "context",
    "elements": [
      {
        "type": "mrkdwn",
        "text": "📅 {{ $json.received_at }} | 📱 {{ $json.phone }}"
      }
    ]
  }
]
```

## Key Concepts Learned
- How to connect Slack to n8n via OAuth2 authentication
- How Slack bot token scopes work and why they matter
- How to use Tally webhooks to send form data to n8n
- How to use Slack Block Kit for rich message formatting
- How to validate Block Kit JSON before pasting into n8n
- How to use IF node for channel routing based on field values
- How to add computed fields (timestamp, ref number) with 
  Edit Fields node
- Why production webhook URL differs from test webhook URL

## Debugging Lessons

### Challenge 1 — Slack OAuth Scope Errors
Connecting Slack to n8n failed with a scope error during 
the OAuth flow. n8n's auto-created Slack app was missing 
required bot token scopes.

Fix: Go to api.slack.com/apps → find the n8n app → 
OAuth & Permissions → Bot Token Scopes → add:
- chat:write
- chat:write.public
- channels:read
Then click Reinstall to Workspace and reconnect in n8n.

### Challenge 2 — Slack Node Block Kit Issues
The Slack node accepted the Block Kit JSON but the message 
wasn't rendering correctly. Block Kit requires perfectly 
valid JSON — a single missing comma or misplaced bracket 
breaks the entire message without a clear error.

Fix: Paste Block Kit JSON into jsonlint.com before adding 
it to n8n. This catches syntax errors instantly. Also 
confirm the Message Type field in the Slack node is set 
to "Blocks" not "Text" — plain text mode ignores all 
Block Kit formatting entirely.

## Business Value
- Zero delay between form submission and team notification
- High-value leads flagged and escalated automatically
- Rich formatted messages with all details in one place
- Works 24/7 — fires even outside business hours
- Reply button opens email to prospect in one click
- Unique reference number on every inquiry for tracking

## Real Business Use Cases
- Digital agencies — instant notification for new project inquiries
- Consultancies — route high-budget leads to senior team members
- E-commerce — notify team of wholesale or bulk order inquiries
- Clinics — alert staff of new patient appointment requests
- Freelancers — never miss a client inquiry again

## How This Fits the Series
```
[Project 04 — Email Notification]
HOT leads trigger urgent Gmail notification
            +
[Project 08 — Slack Notification]
ALL inquiries trigger instant Slack notification
            =
Combined: Every lead and inquiry reaches the 
right person through the right channel instantly
