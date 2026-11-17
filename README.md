# AI-Powered Collections Voice Agent

An AI-powered voice assistant for handling loan repayment conversations, customer verification, payment commitments, and human-agent escalation through automated voice interactions.

## Key Features

- **Customer Verification:** Verifies customer identity before disclosing account information.
- **Account Retrieval:** Fetches loan and repayment details after successful verification.
- **Promise-to-Pay:** Records customer payment commitments.
- **Payment Links:** Simulates payment-link delivery through SMS/WhatsApp.
- **Human Escalation:** Routes financial hardship cases and unresolved disputes to human agents.
- **Call Disposition:** Records conversation outcomes.
- **Multilingual Support:** Handles Hindi and English conversations.

## Technology Stack

| Component | Technology |
|---|---|
| Voice Platform | Vapi |
| LLM | GPT-4o |
| Speech-to-Text | Deepgram Nova-2 |
| Text-to-Speech | ElevenLabs Turbo v2.5 |
| Backend | Node.js, Express.js |
| Integration | Webhooks, Function Calling |

## System Architecture

The system connects voice processing, conversational intelligence, and backend operations.

1. Customer initiates a voice conversation.
2. Vapi processes speech and GPT-4o interprets the request.
3. The assistant invokes the appropriate backend tool.
4. Express processes the request and returns a structured response.
5. The assistant responds using synthesized speech.
6. The call outcome is recorded through the disposition tool.

Architecture and conversation state diagrams are available in the `diagrams/` directory.

## Backend Tool Integration

The Express server implements six tools using an in-memory mock customer record.

| Tool | Purpose |
|---|---|
| `verify_customer` | Validates customer identity |
| `get_account_details` | Retrieves verified loan details |
| `log_promise_to_pay` | Records payment commitments |
| `send_payment_link` | Simulates payment-link delivery |
| `escalate_to_agent` | Handles human escalation |
| `mark_disposition` | Records call outcomes |

### Authentication and Conversation Flow

- Account information is accessible only after successful customer verification.
- The system prompt enforces authentication before disclosure.
- The backend independently validates verification status, preventing unauthorized account access.
- Different conversation paths handle failed verification, wrong-person responses, payment disputes, financial hardship, and escalation.

## Project Structure

```text
Voice_agent_vapi/
├── HLD_Collections_Voicebot.docx
├── diagrams/
│   ├── architecture.png
│   └── state_machine.png
├── prompts/
│   └── system_prompt.md
├── tools_schema.json
├── server/
│   ├── server.js
│   └── package.json
└── README.md
```

## Getting Started

### Prerequisites

- Node.js and npm
- Vapi assistant
- ngrok for local webhook exposure

### 1. Start the Backend

```bash
cd server
npm install
node server.js
```

The Express server starts on port `3000`.

### 2. Expose the Server

Start an ngrok tunnel in another terminal:

```bash
ngrok http 3000
```

Use the generated HTTPS address as the public webhook base URL.

### 3. Configure Vapi

Configure the assistant with:

| Setting | Configuration |
|---|---|
| Model | GPT-4o |
| Voice | ElevenLabs Turbo v2.5 |
| Transcriber | Deepgram Nova-2 |
| System Prompt | `prompts/system_prompt.md` |
| Tools | `tools_schema.json` |

Configure all six tools with their corresponding webhook endpoints and replace the webhook host with your public server URL.

## Challenges and Engineering Decisions

**Webhook Payload Handling:** Implemented an `extractArgs()` utility to handle different Vapi tool-call payload structures, including string and object arguments. Added a flat-body fallback for independent endpoint testing.

**Authentication Enforcement:** Strengthened the verification flow by combining prompt-level restrictions with independent backend validation to prevent sensitive account information from being disclosed to unverified callers.

## Limitations and Future Improvements

- Replace in-memory mock data with persistent database and LMS integration.
- Integrate real SMS/WhatsApp payment delivery.
- Implement SIP-based human-agent transfer.
- Add webhook signature verification, idempotency, and rate limiting.
- Introduce persistent audit logging and conversation history.

## Mock Data

The backend currently uses an in-memory customer record.

| Field | Value |
|---|---|
| Customer | Rahul Sharma |
| Loan ID | KF-PL-778821 |
| EMI | ₹8,499 |
| Days Past Due | 12 |

Payment-link delivery is simulated through server-side logging rather than a live payment gateway.
