# Project 07 — FAQ Chatbot With Intent Recognition (Voiceflow)

## Overview
A fully functional FAQ chatbot built in Voiceflow for a fictional 
digital consulting business called GrowthPath. Unlike button-only 
chatbots, this bot uses intent recognition — understanding what 
a user means regardless of how they phrase their question. Users 
type naturally. The bot listens, understands, and responds 
correctly every time.

## Tools Used
- Voiceflow (chatbot builder + NLU engine)

## What Makes This Different From Project 01
Project 01 was a structured lead capture bot — users followed 
a fixed path of buttons and inputs. Project 07 is an intelligent 
FAQ bot — users type anything naturally and the bot understands 
their intent and routes to the correct answer automatically.

## The 6 Intents Built

| Intent Name | Example Utterances |
|---|---|
| pricing_inquiry | "How much does it cost?" / "What are your rates?" / "Is it expensive?" |
| services_inquiry | "What do you offer?" / "What services do you have?" |
| contact_inquiry | "How do I reach you?" / "What's your email?" |
| location_inquiry | "Where are you based?" / "What's your address?" |
| turnaround_inquiry | "How long does it take?" / "What's the timeline?" |
| booking_inquiry | "How do I book?" / "Can I schedule a call?" |

Each intent was trained with 8+ utterances covering formal, 
casual, short and long phrasings to maximise recognition accuracy.

## Workflow Structure
```
[Welcome Message]
        ↓
[Choice Block — Intent Listening Mode]
  ├── pricing_inquiry ──→ [Pricing Answer] ──→ [Follow-up Choice]
  ├── services_inquiry ──→ [Services Answer] ──→ [Follow-up Choice]
  ├── contact_inquiry ──→ [Contact Answer] ──→ [Follow-up Choice]
  ├── location_inquiry ──→ [Location Answer] ──→ [Follow-up Choice]
  ├── turnaround_inquiry ──→ [Turnaround Answer] ──→ [Follow-up Choice]
  ├── booking_inquiry ──→ [Booking Answer] ──→ [Follow-up Choice]
  └── No Match ──→ [Reprompt + Buttons] ──→ [Fallback + Human Contact]
                                    ↑
        [Follow-up "Ask another question"] loops back here
```

## How Intent Recognition Works
When a user types anything, Voiceflow's NLU engine:
1. Reads the user's message
2. Compares it against all trained intent utterances
3. Matches it to the closest intent
4. Follows that intent's path automatically

No buttons required. No exact phrasing required.

## No Match Handler — Two Layer Fallback
Layer 1 — After first failed match:
"I'm sorry, I didn't catch that. I can help with 
services, pricing, booking, turnaround, location, 
or contact. Could you rephrase? Or choose below:"
[Shows FAQ buttons as fallback]

Layer 2 — After 2 failed matches:
"It seems I'm having trouble understanding. Let me 
connect you with our team directly.
📧 hello@growthpath.com"

## Loop Back Logic
After every answer, the user sees:
- "Ask another question" → loops back to main Choice block
- "Book a consultation" → routes to booking flow
- "No thanks" → goodbye message

The loop goes back to the Choice block — NOT the welcome 
message — so the greeting never repeats mid-conversation.

## Test Results

| Test | Input | Expected | Result |
|---|---|---|---|
| Exact match | "What are your prices?" | Pricing answer | ✅ |
| Paraphrase | "How much would it cost to work with you?" | Pricing answer | ✅ |
| Casual | "yo how much is this gonna cost me" | Pricing answer | ✅ |
| No match | "What is the meaning of life?" | Reprompt + buttons | ✅ |
| Multi-turn | Pricing → ask again → Services | Smooth loop | ✅ |
| All 6 intents | Varied phrasings | Correct routing | ✅ |

## Key Concepts Learned
- What intents are and how Voiceflow's NLU engine works
- How to create and train intents in the Voiceflow CMS
- How to configure a Choice block in intent listening mode
  (different from button mode — not immediately obvious)
- Why utterance variety matters more than utterance quantity
- How to enable and configure the No Match handler
- How to set reprompt limits before triggering final fallback
- How to build loop back logic without repeating the welcome message
- How to test intents with casual and unexpected phrasing
- The difference between a scripted bot and an intelligent one

## Debugging Lessons
- The Choice block behaves differently in intent mode vs 
  button mode — switching between the two requires specific 
  configuration steps that aren't immediately obvious
- Intent routing didn't work correctly until the Choice block 
  was fully reconfigured for intent listening mode
- Utterances that were too similar to each other caused 
  occasional misrouting — adding more varied phrasings fixed it

## Business Value
- Available 24/7 — answers questions instantly at any hour
- Consistent — always gives the same accurate answer
- Natural — users type freely without needing to follow a script
- Scalable — handles unlimited simultaneous users
- Extendable — can be connected to Project 01 lead capture 
  flow at the end of the FAQ conversation

## Real Business Use Cases
- Digital agencies — answer service and pricing questions
- Consultancies — qualify prospects before booking calls
- E-commerce stores — answer product and shipping questions
- Healthcare clinics — answer appointment and policy questions
- SaaS products — answer feature and pricing questions

## How This Fits the Series
```
[Project 01 — Lead Capture Bot]
Structured conversation, button-driven
            +
[Project 07 — FAQ Intent Bot]
Free-form conversation, intent-driven
            =
Combined: A complete chatbot that both answers 
questions intelligently AND qualifies leads automatically
