# Sarvam API Triage & DevRel Agent

An AI-powered support and triage agent designed for [Sarvam AI](https://docs.sarvam.ai) developer tools. 

Built using [Lyzr Agent Studio](https://studio.lyzr.ai) and powered by Claude Sonnet, this agent acts as an automated first-line support engineer. It answers technical developer questions using official documentation and automatically flags high-priority business inquiries for human support.

---

## Why This Project Exists

When developer platforms grow, they face two common support problems:
1. **Repetitive Questions:** Support engineers spend most of their time answering basic setup and syntax questions.
2. **Lost Business Leads:** Enterprise customers asking for custom pricing or dedicated infrastructure often get stuck waiting behind routine questions.
3. **Chatbot Hallucinations:** Standard chatbots often make up non-existent features or wrong code when they do not know the answer.

### The Solution
This agent sits between incoming questions and the support team. It:
* Answers everyday questions accurately using verified Sarvam documentation.
* Identifies who is asking (developer, architect, or business lead).
* Assigns an urgency rating (P0 critical to P2 routine).
* Immediately flags queries it cannot answer so human teams can take over.

---

## How It Works
Incoming User Query
│
▼
┌────────────────────────────────────────────────────────┐
│               Lyzr Agent Studio Core                   │
│                                                        │
│  1. Check Persona                                      │
│     (developer, enterprise_architect, product_lead)    │
│                                                        │
│  2. Assess Urgency                                     │
│     (P0_critical, P1_enterprise, P2_routine)           │
│                                                        │
│  3. Search Knowledge Base (kb_sarvam_dev_docs)         │
│     (Speech-to-Text, Translation, TTS, Auth)           │
│                                                        │
│  4. Apply Guardrails                                   │
│     Is the answer in the docs?                         │
│     ├── YES: Generate accurate code/explanation        │
│     └── NO:  Set escalation_required = true            │
└────────────────────────────────────────────────────────┘
│
▼
Structured JSON Output (Ready for Slack, Discord, or Zendesk)
---

## Key Features

* **Grounded in Official Docs:** Uses Sarvam AI documentation for Speech-to-Text (Saaras), Text-to-Speech (Bulbul), Translation, and Document Intelligence.
* **Zero Guesswork (Anti-Hallucination):** If an inquiry asks about unreleased features, custom discounts, or private server deployments, the agent does not guess. It sets `escalation_required: true`.
* **Clean JSON Output:** Instead of unformatted chat text, the agent returns structured data so ticketing systems (like Jira, Zendesk, or Slack bots) can automatically route tickets.

---

## Real Output Examples

### Example 1: Routine Developer Question (Resolved Automatically)
**User Query:** *"How do I call speech-to-text for Telugu audio in Python?"*

```json
{
  "routing_metadata": {
    "detected_persona": "developer",
    "urgency_tier": "P2_routine_inquiry",
    "target_module": "speech_to_text",
    "escalation_required": false
  },
  "technical_response": "Use the POST /speech-to-text endpoint with model 'saaras:v2' or 'saaras:v4' and language_code 'te-IN'. Example:\n\nimport requests\n\nheaders = {'api-subscription-key': 'YOUR_KEY'}\nfiles = {'file': open('audio.wav', 'rb')}\ndata = {'model': 'saaras:v4', 'language_code': 'te-IN'}\n\nres = requests.post('[https://api.sarvam.ai/speech-to-text](https://api.sarvam.ai/speech-to-text)', headers=headers, files=files, data=data)\nprint(res.json())"
}

## Real Output Examples

### Example 2: Enterprise Lead (Escalated to Human Team)
**User Query:** *"We process 50 million minutes per month. Can we run this model on an air-gapped on-premise server? What is the enterprise pricing?"*
```json
{
  "routing_metadata": {
    "detected_persona": "enterprise_architect",
    "urgency_tier": "P1_enterprise_escalation",
    "target_module": "general_auth",
    "escalation_required": true
  },
  "technical_response": "Air-gapped on-premise deployments and custom high-volume pricing require enterprise contracts not covered in public docs. This request has been flagged for the Enterprise Solutions and Sales Architecture team."
}

## Technical Details 
Platform: Lyzr Agent Studio

Model: Claude Sonnet

Knowledge Base: kb_sarvam_dev_docs (Sarvam AI documentation)

Prompt & Schema: View full instructions in agent_instructions.md

## Project Background & Learnings
Built during the Lyzr AI Agent Labs in Visakhapatnam.

The primary learning from this project: The most important part of an enterprise AI agent is not the chat prompt—it is the boundary logic. Making an agent reliably say "I don't know, let me connect you to our human team" is what turns a prototype into software a business can trust.

Author
Ashish Nadh Varri - LinkedIn Profile • GitHub
