# Project 05 — AI Text Summariser Workflow (n8n + OpenAI)

## Overview
A n8n workflow that accepts any long piece of text, sends it 
to OpenAI with a structured summarisation prompt, and delivers 
a clean formatted summary directly to an email inbox via Gmail. 
This is the first project in the series where OpenAI becomes 
the core engine of the workflow — not just a supporting tool.

## Tools Used
- n8n Cloud (automation engine)
- OpenAI API — gpt-4o-mini (AI summarisation)
- Gmail node (HTML email delivery)

## Workflow Structure
```
[Manual Trigger]
      ↓
[Set Node] — holds the input text
      ↓
[OpenAI Node] — sends text + prompt, receives summary
      ↓
[Edit Fields] — extracts summary from OpenAI response
      ↓
[Gmail] — delivers HTML formatted summary to inbox
```

## How It Works
1. Manual trigger fires the workflow
2. Set node holds the long input text as a variable
3. OpenAI node receives the text alongside a system prompt
   that instructs it to return a structured summary
4. Edit Fields node extracts just the summary content from
   the full OpenAI response object
5. Gmail node sends the summary as a formatted HTML email

## OpenAI Node Configuration
- Resource: Text
- Operation: Message a Model
- Model: gpt-4o-mini

## System Prompt Used
```
You are a professional content summariser. When given a piece 
of text, you will return a structured summary in the following 
format:

Summary:
A 2-3 sentence overview of the main point.

Key Points:
- Point 1
- Point 2
- Point 3
- Point 4
- Point 5

Conclusion:
One sentence on the overall takeaway.

Always be concise, accurate and professional. Never add 
information that was not in the original text.
```

## User Message Expression
```
Please summarise the following text:

{{ $json.text }}
```

## Output Format
The summary is delivered as an HTML formatted email containing:
- A structured summary paragraph
- 5 key bullet points
- A one sentence conclusion
- Auto-generated timestamp

## Example Output
```
Summary:
Businesses adopting AI automation in 2026 are gaining 
significant competitive advantages through cost reduction 
and increased operational efficiency.

Key Points:
- AI automation reduces repetitive task costs by up to 40%
- Companies report 3x faster processing after implementation
- Customer service improves significantly with AI support
- Implementation challenges include training and data quality
- Early adopters gain advantage over slower-moving rivals

Conclusion:
Businesses that delay AI adoption risk falling behind 
competitors already leveraging automation to scale faster.
```

## Key Concepts Learned
- How to configure the OpenAI node in n8n correctly
- The difference between Resource, Operation and Model settings
- How System and User messages work together in a prompt
- How to write effective system prompts for consistent output
- How to use {{ $json.text }} to pass dynamic content to OpenAI
- How to extract the response using $json.message.content
- How to switch fields between plain text and expression mode
- How to format and deliver AI output via HTML email

## Debugging Lessons
- Resource must be set to "Text" and Operation to 
  "Message a Model" before System and User fields appear
- The {} expression toggle must be activated before 
  expressions like {{ $json.text }} will work in fields
- $json.message.content is the correct path to extract 
  just the summary text from the full OpenAI response object

## Real Business Use Cases
- Law firms summarising long contracts instantly
- Marketing agencies summarising competitor content
- Sales teams summarising lengthy email threads before calls
- Content creators summarising research for content planning
- Recruiters summarising long CVs into quick overviews
