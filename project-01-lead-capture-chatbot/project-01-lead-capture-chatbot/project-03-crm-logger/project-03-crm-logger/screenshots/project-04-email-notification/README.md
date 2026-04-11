# Project 04 — Instant Email Notification System for New Leads

## Overview
A n8n workflow that fires an instant email notification 
the moment a new lead is received — routing the right alert 
to the right person based on lead score. HOT leads trigger 
an urgent notification. WARM and COLD leads trigger a 
standard notification. All automatically, with zero human 
involvement.

## Tools Used
- n8n Cloud (automation engine)
- Gmail node (email delivery via OAuth)
- IF node (lead score routing)
- Google Sheets (lead data source from Project 03)

## The Problem This Solves
A HOT lead submits their details at 9am.
The sales rep checks the CRM at 2pm.
The lead has already spoken to a competitor.

This workflow makes sure that never happens. The moment 
a lead comes in, the right person is notified instantly 
with everything they need to act.

## Workflow Structure
```
[Webhook] — receives clean lead data
      ↓
[IF Node] — checks lead score
      ↓                    ↓
[HOT path]           [WARM/COLD path]
      ↓                    ↓
[Gmail — urgent]    [Gmail — standard]
notification        notification
```

## How It Works
1. Clean lead data arrives from the Project 02 formatter
2. IF node reads the lead_score field
3. HOT leads (score = "HOT") follow the True path
4. WARM and COLD leads follow the False path
5. Each path triggers a differently formatted HTML email
6. Sales team receives the right notification instantly

## IF Node Configuration
```
Value 1:   {{ $json.body.lead_score }}
Operator:  equals
Value 2:   HOT
```

## HOT Lead Email
- Subject: 🔥 HOT Lead Alert — [Name] is ready to start immediately
- Format: HTML table with all lead details
- Urgency flag: "Follow up within 30 minutes"
- Color: Red (#e74c3c)

## WARM/COLD Lead Email
- Subject: 📋 New Lead — [Name] (WARM/COLD)
- Format: HTML table with all lead details
- Tone: Standard, informational
- Color: Blue (#2980b9)

## Email Fields Included
Every notification email contains:

| Field | Description |
|---|---|
| Name | Lead's full name |
| Email | Lead's email address |
| Phone | International format phone number |
| Business | Service or business type |
| Timeline | When they want to start |
| Lead Score | HOT / WARM / COLD |
| Received At | Exact submission timestamp |

## HTML Email Template — HOT Lead
```html
<h2 style="color: #e74c3c;">🔥 New HOT Lead Received</h2>
<p>A highly qualified lead just came in and needs 
immediate follow-up.</p>
<table style="border-collapse: collapse; width: 100%;">
  <tr>
    <td style="padding: 8px; font-weight: bold;">Name</td>
    <td style="padding: 8px;">{{ $json.body.Name }}</td>
  </tr>
  <tr style="background: #f9f9f9;">
    <td style="padding: 8px; font-weight: bold;">Email</td>
    <td style="padding: 8px;">{{ $json.body.Email }}</td>
  </tr>
  <tr>
    <td style="padding: 8px; font-weight: bold;">Lead Score</td>
    <td style="padding: 8px; color: #e74c3c; 
    font-weight: bold;">HOT</td>
  </tr>
</table>
<p style="color: #e74c3c; font-weight: bold;">
⚡ Follow up within 30 minutes for best conversion rate.
</p>
```

## Key Concepts Learned
- How to connect Gmail to n8n via OAuth authentication
- How OAuth permission scopes work for email sending
- How to use the IF node for conditional workflow routing
- How to build HTML emails inside n8n Gmail node
- How to reference nested JSON fields in IF node conditions
- Why IF node conditions are case-sensitive
- How to set reply-to field for one-click lead response
- How speed of notification directly impacts conversion rate

## Debugging Lessons
- Gmail OAuth authentication requires careful permission 
  setup — rushing the connection causes permission errors
- IF node condition was not reading lead_score correctly 
  initially due to data nesting — field path must be 
  checked carefully in the INPUT panel first
- HTML email body must be set to "HTML" type in Gmail 
  node — plain text mode ignores all HTML formatting

## How Projects 01 Through 04 Connect
```
[Project 01 — Voiceflow Chatbot]
Prospect qualifies through conversation
            ↓
[Project 02 — n8n Webhook Formatter]
Data cleaned, scored and enriched
            ↓
[Project 03 — Google Sheets CRM Logger]
Every lead stored automatically
            ↓
[Project 04 — Email Notification System]
Right person alerted instantly
```
From first website visit to sales team notification —
fully automated. Zero manual steps.

## Business Value
- HOT leads are flagged and escalated within seconds
- Sales team never has to check the CRM manually
- Different urgency levels communicated automatically
- Reply-to set as lead email for instant one-click response
- Works 24/7 — notifications fire even outside office hours

- [x] Added to Portfolio
