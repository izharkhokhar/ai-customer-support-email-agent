# AI Customer Support Email Agent

I built this n8n workflow to automate the first stage of customer support email handling while keeping higher-risk requests under human control.

The workflow monitors a Gmail inbox, uses OpenAI to classify each incoming email, and routes it into one of three paths:

- **AUTO_REPLY** — routine support requests that can receive a safe automated response
- **HUMAN_REVIEW** — complaints, fraud/security concerns, unusual compensation requests, payment disputes, or cases that need a human decision
- **NOT_SUPPORT** — newsletters, marketing messages, automated replies, delivery failures and other non-support email

## What It Does

- Monitors Gmail for new unprocessed messages
- Extracts the email body, subject, sender and message ID
- Uses OpenAI to classify the email
- Generates a concise customer-support reply for suitable routine requests
- Replies directly in the original Gmail conversation
- Routes sensitive or uncertain cases for human review
- Filters out automated/non-support messages
- Uses Gmail labels to prevent the same message being processed repeatedly

## Workflow

```text
Gmail Trigger
    ↓
Edit Content
    ↓
Customer Support Eval
    ↓
Switch
    ├── AUTO_REPLY
    │      ↓
    │   Generate Customer Reply
    │      ↓
    │   Gmail Reply
    │      ↓
    │   AI - Processed
    │
    ├── HUMAN_REVIEW
    │      ↓
    │   AI - Human Review
    │      ↓
    │   AI - Processed
    │
    └── NOT_SUPPORT
           ↓
        AI - Processed
```

## Safety Approach

The classifier is designed to avoid automatically responding to messages such as:

- legal threats
- fraud or serious security issues
- unusual compensation requests
- serious complaints
- large payment disputes
- requests that require information or decisions the AI does not have

The reply-generation prompt also instructs the model not to invent order status, tracking details, refund information, policies, prices, availability or actions that were not actually performed.

## Tech Stack

- n8n
- OpenAI
- Gmail
- OAuth
- LLM-based classification
- Workflow automation

## Repository Structure

```text
ai-customer-support-email-agent/
├── README.md
├── workflows/
│   └── customer-support-email-agent.json
├── screenshots/
│   └── customer-support-email-agent.png
└── docs/
    └── workflow-overview.md
```

## Setup

The workflow file in this repository is a public-safe template. Account-specific credential references, webhook IDs, Gmail label IDs and n8n instance metadata have been removed.

After importing the workflow into n8n:

1. Connect your own Gmail credential.
2. Connect your own OpenAI credential.
3. Create two Gmail labels:
   - `AI - Processed`
   - `AI - Human Review`
4. Open each Gmail **Add Labels** node and select the correct label:
   - all processed branches should use `AI - Processed`
   - the human-review branch should also use `AI - Human Review`
5. Confirm the Gmail Trigger search is:

```text
in:inbox -label:"AI - Processed"
```

6. Select a compatible OpenAI model if the imported model is not available in your account.
7. Test each route with sample emails before activating the workflow.

## Notes

This is a portfolio/demo project showing how AI can be used for email triage and routine customer-support automation while keeping sensitive cases in a human-review path.

For a production deployment I would also add stronger observability, error handling, retry logic, support-system/CRM integration and organisation-specific policy/knowledge sources.
