# Sarvam API Triage & DevRel Agent

An AI-powered support and triage router designed for [Sarvam AI](https://docs.sarvam.ai) developer tools. 

Built during **Lyzr AI Agent Labs (Vizag)** using [Lyzr Agent Studio](https://studio.lyzr.ai) and powered by Claude Sonnet, this agent acts as an automated first-line support engineer. It answers technical developer questions using official documentation and automatically flags high-priority business inquiries for human teams.

---

## The Problem & Solution

### The Problem
When developer platforms grow, they run into three major support challenges:
1. **Repetitive Questions:** Support engineers spend most of their time answering basic setup, syntax, and language code questions.
2. **Delayed High-Value Deals:** Enterprise clients asking about custom volume pricing or private deployments often wait days behind simple routine questions.
3. **Chatbot Hallucinations:** Standard chatbots often invent non-existent API parameters or pricing plans when they do not know the answer.

### The Solution
The **Sarvam API Triage & DevRel Agent** sits between incoming questions and the internal engineering team:
* Resolves routine technical questions using official documentation.
* Detects user persona (`developer`, `enterprise_architect`, or `product_lead`).
* Assigns urgency tiers (`P0_critical`, `P1_enterprise_escalation`, or `P2_routine_inquiry`).
* Enforces strict boundary logic: if an inquiry is out of scope (e.g., custom pricing or air-gapped VPCs), it marks the request for human escalation instead of guessing.

---

## How It Works

```text
[ Incoming Community / Developer Query ]
                    │
                    ▼
┌────────────────────────────────────────────────────────┐
│               Lyzr Agent Studio Core                   │
│                                                        │
│  1. Identify Persona                                   │
│     (developer | enterprise_architect | product_lead)  │
│                                                        │
│  2. Classify Urgency                                   │
│     (P0_critical | P1_enterprise_escalation | P2)      │
│                                                        │
│  3. Retrieve Grounded Documentation                    │
│     Knowledge Base: kb_sarvam_dev_docs                 │
│     (Speech-to-Text, Translation, TTS, Auth)           │
│                                                        │
│  4. Enforce Boundary Logic                             │
│     Is the answer documented in public docs?           │
│     ├── YES ──► Generate grounded technical response   │
│     └── NO  ──► Set escalation_required = true         │
└────────────────────────────────────────────────────────┘
                    │
                    ▼
┌────────────────────────────────────────────────────────┐
│              Structured JSON Output                    │
│  Ready for webhook dispatch to Discord, Slack, or Jira │
└────────────────────────────────────────────────────────┘
```

---

## Key Features

* **Grounded in Official Documentation:** Retrieval is strictly tied to `kb_sarvam_dev_docs`, covering Sarvam's speech-to-text (Saaras), text-to-speech (Bulbul), translation, and document intelligence models.
* **Deterministic Boundary Logic (Zero Hallucination):** The agent never invents API parameters, private pricing discounts, or unreleased models. Undocumented inquiries immediately trigger an escalation flag.
* **Structured JSON Output:** Returns consistent, machine-readable JSON rather than plain conversational text, making it directly compatible with automated webhook workflows in Zendesk, Slack, or CRM tools.

---

## Output Examples

### Example 1: Routine Developer Inquiry (Resolved Automatically)
**User Query:** *"How do I call speech-to-text for Telugu audio in Python?"*

```json
{
  "routing_metadata": {
    "detected_persona": "developer",
    "urgency_tier": "P2_routine_inquiry",
    "target_module": "speech_to_text",
    "escalation_required": false
  },
  "technical_response": "Use the POST /speech-to-text endpoint with model 'saaras:v2' or 'saaras:v4' and language_code 'te-IN'. Example implementation:\n\nimport requests\n\nheaders = {'api-subscription-key': 'YOUR_API_KEY'}\nfiles = {'file': open('audio.wav', 'rb')}\ndata = {'model': 'saaras:v4', 'language_code': 'te-IN'}\n\nres = requests.post('[https://api.sarvam.ai/speech-to-text](https://api.sarvam.ai/speech-to-text)', headers=headers, files=files, data=data)\nprint(res.json())"
}
```

### Example 2: Enterprise Inquiry (Escalated to Human Team)
**User Query:** *"We process 50 million minutes per month. Can we run this model on an air-gapped on-premise server? What is the enterprise pricing?"*

```json
{
  "routing_metadata": {
    "detected_persona": "enterprise_architect",
    "urgency_tier": "P1_enterprise_escalation",
    "target_module": "general_auth",
    "escalation_required": true
  },
  "technical_response": "Air-gapped on-premise deployments and custom high-volume pricing require custom enterprise agreements not covered in public documentation. This request has been flagged for the Enterprise Solutions and Sales Architecture team."
}
```

---

## Technical Configuration

* **Orchestration Platform:** [Lyzr Agent Studio](https://studio.lyzr.ai)
* **Underlying Model:** Claude Sonnet
* **Knowledge Base:** `kb_sarvam_dev_docs` (Sarvam AI documentation)
* **Agent Role:** Technical Support Engineer & API Triage Router
* **System Prompt & Schema:**

```text
OPERATIONAL CONSTRAINTS:
1. Grounding: Rely strictly on the context retrieved from the Sarvam AI documentation Knowledge Base. If the required information is missing, unverified, or out of scope, DO NOT speculate. Mark "escalation_required": true.
2. Output Format: You MUST always respond strictly with a valid JSON object matching the exact schema below. Do not wrap the JSON in markdown code fences or add conversational chit-chat outside the JSON structure.

JSON OUTPUT SCHEMA:
{
  "routing_metadata": {
    "detected_persona": "developer | enterprise_architect | product_lead",
    "urgency_tier": "P0_critical | P1_enterprise_escalation | P2_routine_inquiry",
    "target_module": "speech_to_text | text_to_speech | translation | doc_agents | general_auth",
    "escalation_required": boolean
  },
  "technical_response": "Grounded, precise explanation including code blocks or endpoint paths where applicable. If escalated, explain what internal team needs to handle this."
}
```

---

## Project Background & Engineering Takeaways

Built during the **Lyzr AI Agent Labs** in Visakhapatnam.

The core learning from this project: **The most critical component of an enterprise agent is its boundary logic.** Setting strict limits on when an agent says *"I don't know, escalating this to our human team"* is what turns an experimental prototype into dependable business infrastructure.

---

## Author
* **Ashish Nadh Varri** - [LinkedIn Profile](https://www.linkedin.com/in/ashishnadhvarri) • [GitHub](https://github.com/Ashish3692003)
