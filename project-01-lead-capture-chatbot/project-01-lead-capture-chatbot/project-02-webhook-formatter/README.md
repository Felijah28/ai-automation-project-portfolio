# Project 02 — Webhook Data Receiver & Formatter in n8n

## Overview
An n8n workflow that receives raw incoming data from a Voiceflow 
chatbot via webhook, cleans it, enriches it, and outputs a 
fully structured payload ready for any CRM, email system, 
or automation downstream. This project picks up directly 
where Project 01 ends.

## Tools Used
- n8n Cloud (automation engine)
- JavaScript (Code nodes)
- Google Sheets node (output destination)

## The Problem This Solves
Raw webhook data is almost never clean. Names arrive in wrong 
casing, emails have extra spaces, phone numbers are in local 
format, and fields sometimes arrive empty. Passing dirty data 
downstream breaks everything. This workflow fixes all of that 
automatically before the data goes anywhere.

## Workflow Structure
```
[Webhook] — receives raw lead data from Voiceflow
     ↓
[Edit Fields] — field mapping, email cleaning, timestamp
     ↓
[Code — Name Capitalisation] — formats name correctly
     ↓
[Code — Lead Scoring] — scores lead by timeline
     ↓
[Code — Phone Formatter] — converts to international format
     ↓
[Code — Missing Fields Handler] — fills empty fields safely
     ↓
[Output] — clean, structured, enriched payload
```

## What Each Node Does

### Edit Fields Node
- Maps all incoming fields to clean output fields
- Trims whitespace from email using `.trim()`
- Converts email to lowercase using `.toLowerCase()`
- Stamps exact date and time of submission automatically

### Code Node 1 — Name Capitalisation
Formats the lead's name correctly regardless of how 
they typed it.

Input:  `"fagbemi elijah"`
Output: `"Fagbemi Elijah"`
```javascript
const name = $input.item.json.body.Name;

const capitalised = name
  .toLowerCase()
  .split(' ')
  .map(word => word.charAt(0).toUpperCase() + word.slice(1))
  .join(' ');

$input.item.json.body.Name = capitalised;

return $input.item;
```

### Code Node 2 — Lead Scoring
Automatically scores every lead as HOT, WARM, or COLD 
based on their timeline response.

- Immediately → HOT
- Within 1 month → WARM
- Just exploring → COLD
```javascript
const timeline = $input.item.json.body.Timeline;

let lead_score;

if (timeline === "Immediately") {
  lead_score = "HOT";
} else if (timeline === "Within 1 month") {
  lead_score = "WARM";
} else {
  lead_score = "COLD";
}

$input.item.json.body.lead_score = lead_score;

return $input.item;
```

### Code Node 3 — Phone Formatter
Converts local Nigerian numbers to international format.

Input:  `09063924403`
Output: `+2349063924403`
```javascript
const phone = $input.item.json.body["Phone number"];
let formatted = phone.replace(/\s+/g, '');

if (formatted.startsWith('0')) {
  formatted = '+234' + formatted.slice(1);
}

$input.item.json.body["Phone number"] = formatted;

return $input.item;
```

### Code Node 4 — Missing Fields Handler
If any field arrives empty or undefined, fills it with 
a safe default value instead of crashing the workflow.
```javascript
const item = $input.item.json.body;

item.Name = item.Name || "Unknown";
item.Email = item.Email || "No email provided";
item["Phone number"] = item["Phone number"] || "No phone provided";
item.business = item.business || "Not specified";
item.Timeline = item.Timeline || "Not specified";
item.lead_score = item.lead_score || "COLD";
item.status = item.status || "New";

return $input.item;
```

## Data Transformation — Before vs After

### Raw Input
```json
{
  "Name": "fagbemi elijah",
  "Email": "  Elijah@Example.COM  ",
  "Phone number": "09063924403",
  "business": "AI Automation",
  "Timeline": "Immediately"
}
```

### Clean Output
```json
{
  "Name": "Fagbemi Elijah",
  "Email": "elijah@example.com",
  "Phone number": "+2349063924403",
  "business": "AI Automation",
  "Timeline": "Immediately",
  "lead_score": "HOT",
  "status": "New",
  "submitted_at": "2026-04-01T22:09:00"
}
```

## Key Concepts Learned
- How to inspect and navigate nested JSON data in n8n
- How to use the Edit Fields node for basic transformations
- How to write JavaScript in n8n Cloud Code nodes
- The correct syntax for n8n Cloud: `$input.item.json.body`
- How field names with spaces require square bracket notation
- Why you must always check the INPUT panel before coding
- How to debug node by node rather than all at once
- How to handle undefined fields gracefully with fallback values

## Debugging Lessons
This project involved several errors that were valuable learning
moments:

- `$input.item.json.full_name` failed because the actual 
  field name was `Name` not `full_name`
- All fields were nested inside `body` — not at the top level
- `Phone number` required `["Phone number"]` syntax due to 
  the space in the field name
- n8n Cloud requires `$input.item.json` not `items[0].json`

## How Projects 01 and 02 Connect
```
[Project 01 — Voiceflow Chatbot]
Collects lead data via conversation
Sends raw JSON to n8n via webhook
            ↓
[Project 02 — n8n Webhook Formatter]
Receives raw data
Cleans, formats and enriches every field
Outputs structured payload for CRM
```

## Screenshots
### Full n8n Workflow
![Full Workflow](screenshots/n8n-full-workflow.png)

### Edit Fields Node
![Edit Fields](screenshots/edit-fields-node.png)

### Name Capitalisation Code Node
![Name Code](screenshots/code-node-name.png)

### Lead Scoring Code Node
![Scoring Code](screenshots/code-node-lead-scoring.png)

### Phone Formatter Code Node
![Phone Code](screenshots/code-node-phone-formatter.png)

### Missing Fields Handler Code Node
![Missing Fields Code](screenshots/code-node-missing-fields.png)

### Clean Output Result
![Clean Output](screenshots/clean-output-result.png)

## Status
- [x] Completed
- [x] Added to Portfolio
