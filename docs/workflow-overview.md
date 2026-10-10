# Workflow Overview

## Purpose

The workflow automates first-line customer support email triage.

It is designed to reduce repetitive inbox handling while avoiding automatic responses in cases that require judgement, sensitive decisions or information the model does not have.

## 1. Gmail Trigger

The workflow checks the inbox for messages that do not already have the `AI - Processed` label.

```text
in:inbox -label:"AI - Processed"
```

This label is used as loop protection so the same email is not repeatedly processed.

## 2. Edit Content

The workflow extracts the values needed by later nodes:

- email body
- thread ID
- message ID
- subject
- sender

## 3. Customer Support Evaluation

OpenAI classifies the message into exactly one of:

- `AUTO_REPLY`
- `HUMAN_REVIEW`
- `NOT_SUPPORT`

The classification prompt explicitly sends automated replies, newsletters, marketing messages, delivery failures, no-reply messages and similar system mail to `NOT_SUPPORT`.

## 4. AUTO_REPLY

Routine support requests are passed to a second OpenAI node that writes a short customer-support response.

The response prompt prevents the model from inventing:

- order or tracking status
- delivery dates
- refund status
- company policies
- prices
- stock information
- actions that were not actually performed

The workflow replies in Gmail and then applies the `AI - Processed` label.

## 5. HUMAN_REVIEW

Higher-risk or uncertain messages receive the `AI - Human Review` label and are also marked `AI - Processed`.

Examples include:

- legal threats
- fraud/security issues
- unusual compensation requests
- serious complaints
- large payment disputes
- cases requiring a human decision

## 6. NOT_SUPPORT

Non-support or automated mail is marked `AI - Processed` without generating a customer reply.

## Public Template

Account-specific Gmail label IDs, credentials, webhook IDs and n8n instance/workflow identifiers have been removed from the public JSON.

After import, users must reconnect Gmail/OpenAI and configure the Gmail label nodes with their own labels.
