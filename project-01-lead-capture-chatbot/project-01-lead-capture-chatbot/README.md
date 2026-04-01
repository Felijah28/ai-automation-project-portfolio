# Project 01 — Multi-Step Lead Capture Chatbot

Overview
A conversational AI chatbot built in Voiceflow that collects 
lead information step by step and sends clean structured data 
to n8n via webhook for processing.

Tools Used
- Voiceflow (chatbot builder)
- n8n (webhook receiver)

What It Collects
- Full name
- Email address
- Phone number
- Business interest
- Timeline

How It Works
1. User opens the chatbot on a website
2. Bot greets the user and asks qualifying questions
3. Text answers are captured via Capture steps
4. Button selections are mapped to variables via Set steps
5. All 6 variables are sent to n8n via a POST request
6. n8n receives a clean JSON payload for further processing

Key Concepts Learned
- Variable creation and management in Voiceflow
- Difference between Capture step (text) and Set step (buttons)
- Correct use of "Variables to set" vs "Properties to set"
- Passing variables to external webhooks via API Request block
- Verifying data flow using Voiceflow debug mode

 Sample n8n Payload
{
  "full_name": "Elijah Fagbemi",
  "email": "elijah@example.com",
  "phone": "08012345678",
  "business": "Automation",
  "timeline": "Within 1 month"
}

Screenshots
See /screenshots folder for:
- Voiceflow canvas overview
- Variable list
- API Request block configuration
- n8n webhook test result showing clean payload
