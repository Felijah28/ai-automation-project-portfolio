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
