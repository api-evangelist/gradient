---
name: Run a chat completion on Gradient Cloud
description: Select an available model and run an OpenAI-compatible chat completion (streaming or non-streaming) against the Gradient Cloud inference API.
api: openapi/gradient-cloud-openapi.yml
operations: [listModels, createChatCompletion]
generated: '2026-07-19'
method: generated
---

# Run a chat completion on Gradient Cloud

Gradient Cloud exposes an OpenAI-compatible inference API. Base URL: `https://apis.gradient.network/api/v1`.

## Auth
- Send your access key as `Authorization: Bearer <access-key>` on every authenticated call.
- `listModels` is public (no auth); `createChatCompletion` requires the access key.

## Steps
1. **Discover models** — call `listModels` (`GET /ai/models`). Pick a model `id` from `data[]` (e.g. `qwen/qwen3-coder-480b-instruct-fp8`).
2. **Send the chat request** — call `createChatCompletion` (`POST /ai/chat/completions`) with `model` and a `messages[]` array of `{role, content}`. Optional: `temperature`, `max_tokens`, `top_p`, `stop`, `presence_penalty`, `frequency_penalty`.
3. **Read the result** — non-streaming responses return `choices[0].message.content` plus a `usage` token count.
4. **Stream (optional)** — set `stream: true` to receive `text/event-stream` `chat.completion.chunk` events; accumulate `choices[0].delta.content` and stop when you receive `data: [DONE]`.

## Rules
- Only use a `model` id returned by `listModels`, or you will get `404 Model Not Found`.
- Handle `429 Rate Limit Exceeded` with exponential backoff (free tier: 60 req/min, 3 concurrent).
- `401` means a missing/invalid access key; `402` means a billing/quota check failed.
- Errors come back as `{ "error": { "message", "type", "code" } }` (OpenAI-style, not RFC 9457).
- There is no idempotency-key contract — do not assume safe automatic retries of non-idempotent writes beyond the documented backoff on 429/5xx.
