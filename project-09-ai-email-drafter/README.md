# Project 09 — AI Email Reply Drafter (n8n + OpenAI + Gmail)

## Overview
A fully automated AI email reply system built in n8n that 
monitors a Gmail inbox, reads every new incoming email, 
decodes and parses its content, categorises it by type, 
generates a contextually appropriate professional reply 
using OpenAI, and automatically sends it — all without 
human involvement. Built in auto-send mode.

## Tools Used
- n8n Cloud (automation engine)
- Gmail Trigger node (inbox monitoring)
- JavaScript Code node (email extraction + base64 decoding)
- Switch node (email categorisation)
- OpenAI node — gpt-4o-mini (reply generation)
- Gmail node (auto-send reply + label processed email)

## The Problem This Solves
The average professional spends 28% of their workday on 
email. Most business emails follow predictable patterns — 
pricing inquiries, support requests, general questions — 
that a well-configured AI can handle consistently and 
instantly. This system handles them automatically, 
responding within 60 seconds regardless of the time of day.

## Workflow Structure
```
[Gmail Trigger — polls every minute]
          ↓
[Code Node — extract sender, subject, decode body]
          ↓
[Switch Node — categorise by subject keywords]
    ↓           ↓           ↓           ↓
[OpenAI]   [OpenAI]   [OpenAI]   [OpenAI]
Pricing    Support    Inquiry    Default
    ↓           ↓           ↓           ↓
[Edit Fields — extract reply + carry fields]
    ↓           ↓           ↓           ↓
[Code — extract sender email address]
    └───────────┴───────────┴───────────┘
                    ↓
          [Gmail — Auto-Send Reply]
                    ↓
          [Gmail — Add AI-Processed Label]
```

## Gmail Trigger Configuration
```
Event:        Message Received
Label:        INBOX
Read Status:  Unread only
Poll Time:    Every minute
```

## Email Extraction Code Node
Handles two key challenges:
1. Email body arrives base64 encoded — must be decoded
2. Sender, subject and date are buried in a headers array

```javascript
const item = $input.item.json;
const headers = item.payload.headers;

const fromHeader = headers.find(h => h.name === 'From');
const subjectHeader = headers.find(h => h.name === 'Subject');
const dateHeader = headers.find(h => h.name === 'Date');

const from = fromHeader ? fromHeader.value : 'Unknown Sender';
const subject = subjectHeader ? subjectHeader.value : 'No Subject';
const date = dateHeader ? dateHeader.value : 'Unknown Date';

let body = '';
if (item.payload.body && item.payload.body.data) {
  body = Buffer.from(item.payload.body.data, 'base64')
    .toString('utf-8');
} else if (item.payload.parts) {
  const textPart = item.payload.parts
    .find(p => p.mimeType === 'text/plain');
  if (textPart && textPart.body && textPart.body.data) {
    body = Buffer.from(textPart.body.data, 'base64')
      .toString('utf-8');
  }
}

$input.item.json.sender = from;
$input.item.json.subject = subject;
$input.item.json.date = date;
$input.item.json.emailBody = body.replace(/\s+/g, ' ').trim();
$input.item.json.emailId = item.id;
$input.item.json.threadId = item.threadId;

return $input.item;
```

## Switch Node — Email Routing Rules
```
Rule 1: subject.toLowerCase() contains "price"  → Pricing path
Rule 2: subject.toLowerCase() contains "help"   → Support path
Rule 3: subject.toLowerCase() contains "inquiry"→ Inquiry path
Rule 4: (fallback)                              → Default path
```

## OpenAI Configuration
- Model: gpt-4o-mini
- Resource: Text
- Operation: Message a Model

### System Prompt — Pricing Inquiries
```
You are a professional sales assistant for GrowthPath, 
a digital consulting business based in Nigeria. 
When responding to pricing inquiries:
- Be warm and professional
- Acknowledge their interest genuinely
- Explain that pricing depends on project scope
- Mention the free consultation as the next step
- Keep the response under 150 words
- Sign off as "The GrowthPath Team"
Never mention specific prices unless a budget is stated.
```

### System Prompt — Support Requests
```
You are a helpful customer support assistant for GrowthPath.
When responding to support requests:
- Acknowledge their issue with empathy
- Ask clarifying questions if the issue is unclear
- Provide helpful guidance if the solution is known
- Offer to escalate to a human team member if needed
- Keep the response professional and concise
- Sign off as "GrowthPath Support Team"
```

### System Prompt — General Inquiries
```
You are a professional assistant for GrowthPath.
When responding to general inquiries:
- Be friendly and welcoming
- Answer the specific question asked
- Highlight relevant services if appropriate
- Include a clear call to action
- Keep the response under 200 words
- Sign off as "The GrowthPath Team"
```

### User Message (All Paths)
```
Please draft a professional reply to this email.

From: {{ $json.sender }}
Subject: {{ $json.subject }}
Date: {{ $json.date }}

Email content:
{{ $json.emailBody }}

Draft a reply that addresses the sender's needs 
appropriately. Do not include a subject line — 
just the email body.
```

## Sender Email Extraction Code
Extracts clean email from "Name <email>" format:

```javascript
const sender = $input.item.json.sender;
const emailMatch = sender.match(/<(.+)>/);
const email = emailMatch ? emailMatch[1] : sender;
$input.item.json.senderEmail = email;
return $input.item;
```

## Gmail Auto-Send Configuration
```
Resource:   Message
Operation:  Send
To:         {{ $json.senderEmail }}
Subject:    Re: {{ $json.subject }}
Message:    {{ $json.draftReply }}
Thread ID:  {{ $json.threadId }}
```

> Thread ID links the reply to the original email 
> conversation. Without it, the AI reply creates a 
> brand new email thread instead of replying inline.

## Gmail Label Configuration
```
Resource:    Message
Operation:   Add Label
Message ID:  {{ $json.emailId }}
Label:       AI-Processed
```
Prevents the same email from being processed again 
on the next poll cycle.

## Key Concepts Learned
- How Gmail Trigger node polls vs real-time webhooks
- Why email bodies arrive base64 encoded in n8n
- How to search through a headers array using .find()
- How to decode base64 with Buffer.from().toString()
- How to handle both simple and multipart email bodies
- How to write role-specific system prompts for consistent output
- Why system prompt quality determines AI output quality
- How Thread ID links replies to original conversations
- How Gmail labels prevent duplicate processing
- The difference between draft mode and auto-send mode

## Debugging Lessons
- Gmail Trigger polls every minute — not instant. 
  Set correct expectations before testing
- Email body is sometimes in payload.body.data and 
  sometimes in payload.parts — the code handles both
- Thread ID is critical for inline replies — always 
  carry it forward from the Gmail Trigger output
- Auto-send mode requires thoroughly tested prompts 
  before activation — the AI replies on behalf of a 
  real business
- OpenAI node requires Resource: Text and 
  Operation: Message a Model before System and User 
  message fields appear (same pattern as Project 05)

## Auto-Send vs Draft Mode
This project is built in auto-send mode. The AI 
generates and sends replies automatically without 
human review.

Recommended approach:
- Start in draft mode — save to Gmail drafts, 
  review manually, send when satisfied
- Switch to auto-send only after testing 20+ emails 
  and confirming reply quality is consistently good
- Keep auto-send limited to specific email categories 
  initially — not all incoming emails

## Business Value
- Responds to emails within 60 seconds — 24/7
- Consistent professional tone on every reply
- Different communication styles per email type
- Never misses an email regardless of time of day
- Frees 2+ hours per day of manual email work
- Scales infinitely — handles 1 or 1000 emails

## Real Business Use Cases
- Consulting businesses — auto-reply to service inquiries
- E-commerce stores — handle order and shipping questions
- Law firms — acknowledge receipt of client emails
- Agencies — respond to new project inquiries instantly
- Freelancers — never leave a client email unanswered

## How This Connects to the Series
```
[Project 04 — Email Notification]
Sends alerts when new leads arrive
            +
[Project 08 — Slack Notification]
Notifies team of new contact form inquiries
            +
[Project 09 — AI Email Reply Drafter]
Responds to incoming emails automatically
            =
Complete intelligent communication system:
Every inquiry is received, notified, and 
replied to — all without human involvement
```
