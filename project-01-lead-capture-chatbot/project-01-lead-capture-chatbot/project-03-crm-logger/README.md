# Project 03 — Automatic CRM Logger (n8n → Google Sheets)

## Overview
An n8n workflow that automatically logs every incoming lead 
into a structured Google Sheets CRM — no manual data entry 
required. This project connects directly to the clean output 
from Project 02, taking the formatted payload and writing it 
as a new row in Google Sheets instantly.

## Tools Used
- n8n (automation engine)
- Google Sheets (CRM database)
- Google Sheets node (native n8n integration)

## What Gets Logged
Every lead that passes through the workflow is recorded with:

| Column | Description |
|---|---|
| Name | Capitalised full name |
| Email | Cleaned, lowercase email |
| Phone number | International format |
| Business | Service or business type |
| Timeline | When they want to start |
| Lead Score | HOT / WARM / COLD |
| Status | New / Contacted / Qualified etc |
| Submitted At | Exact date and time of submission |

## How It Works
1. Clean data arrives from the Project 02 formatter workflow
2. n8n Google Sheets node authenticates via Google OAuth
3. Node maps each field to the correct column in the sheet
4. A new row is appended automatically for every lead
5. The Google Sheet acts as a live, always-updated CRM

## Workflow Structure
## [Webhook] — receives incoming lead data
↓
## [Edit Fields] — maps and cleans fields
↓
## [Code — Name capitalisation]
↓
## [Code — Lead scoring by timeline]
↓
## [Code — Phone formatter]
↓
## [Code — Missing fields handler]
↓
## [Google Sheets — Append Row] — logs lead to CRM

## Key Concepts Learned
- How to connect n8n to Google Sheets via OAuth authentication
- How to use the Append Row operation in the Google Sheets node
- How to map n8n field values to specific sheet columns
- How to structure a Google Sheet to function as a proper CRM
- How Projects 01, 02 and 03 connect into one complete pipeline

## Google Sheets CRM Structure
The sheet is structured with these columns:

| A | B | C | D | E | F | G | H |
|---|---|---|---|---|---|---|---|
| Name | Email | Phone | Business | Timeline | Lead Score | Status | Submitted At |

## Result
Every lead that interacts with the Voiceflow chatbot 
(Project 01) gets their data cleaned (Project 02) and 
automatically stored as a new row in Google Sheets (Project 03) 
— entirely without human involvement.
