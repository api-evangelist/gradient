---
name: Run a text completion on Gradient Cloud
description: Run an OpenAI-compatible text (prompt) completion against a Gradient Cloud model, streaming or non-streaming.
api: openapi/gradient-cloud-openapi.yml
operations: [listModels, createCompletion]
generated: '2026-07-19'
method: generated
---

# Run a text completion on Gradient Cloud

Base URL: `https://apis.gradient.network/api/v1`. Auth: `Authorization: Bearer <access-key>`.

## Steps
1. **Pick a model** — call `listModels` (`GET /ai/models`, public) and choose a model `id`.
2. **Send the prompt** — call `createCompletion` (`POST /ai/completions`) with `model` and `prompt` (string or array). Optional: `suffix`, `max_tokens`, `temperature`, `top_p`, `n`, `stream`.
3. **Read the result** — non-streaming responses return `choices[0].text` and a `usage` token count.
4. **Stream (optional)** — set `stream: true` for a `text/event-stream`; stop at `data: [DONE]`.

## Rules
- Use only model ids from `listModels` (`404 Model Not Found` otherwise).
- Back off on `429`; `401` = bad access key; `402` = billing check failed; `500` = retry with backoff.
- Prefer the chat endpoint (`createChatCompletion`) for conversational use; use text completion for raw prompt continuation.
