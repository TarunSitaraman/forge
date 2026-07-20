# LLM Brain

## Mental Model

The brain is a **model-agnostic fallback chain with a single strict
output contract**. Any of ~6 different models across 3 providers might
answer a given request, but every single one is instructed to return
the exact same JSON schema — so the rest of the system never needs to
know or care which model actually responded.

## The Three-Round Fallback Chain

```
Round 1: Groq (primary — fast, generous free tier)
  ├─ deepseek-r1-distill-llama-70b  (reasoning quality — chain-of-thought)
  ├─ llama-3.3-70b-versatile        (high quality)
  └─ llama-3.1-8b-instant           (fast fallback within Groq itself)
        ↓ (all Groq models cooling down or failed)
Round 2: OpenRouter (secondary)
  ├─ openrouter/owl-alpha
  └─ meta-llama/llama-3.3-70b-instruct:free
        ↓ (all OpenRouter models failed)
Round 3: Gemini (tertiary)
  ├─ gemini-2.0-flash
  └─ gemini-1.5-flash
```

**Per-model cooldown, not per-provider:** `modelFailCache` (a `Map`)
tracks failure timestamps per individual model ID with a 5-minute
cooldown (`MODEL_COOLDOWN_MS`). This is finer-grained than
provider-level circuit breaking — one Groq model rate-limiting doesn't
take the other two Groq models out of rotation.

**Why this ordering:** Groq is fastest and has the most generous free
tier, so it's tried first for both cost and latency reasons. The
DeepSeek R1 reasoning model is tried *first within Groq* specifically
for its chain-of-thought quality on ambiguous intent classification,
with faster/simpler models as same-provider fallback before paying the
cross-provider latency cost of falling through to OpenRouter or Gemini.

## Handling DeepSeek R1's Quirks

R1 doesn't support `json_object` response-format mode like the other
models. Two consequences handled explicitly in `callGroqModel`:

```javascript
const isReasoning = modelId.includes('deepseek-r1');
if (jsonMode && !isReasoning) body.response_format = { type: 'json_object' };
// ...
if (isReasoning) content = content.replace(/<think>[\s\S]*?<\/think>/g, '').trim();
```

R1 emits its chain-of-thought inside `<think>...</think>` tags before
the actual answer — these are stripped via regex before the remaining
text is parsed as JSON. This is a good example of **provider-specific
leakage that the fallback abstraction can't fully hide** — the caller
function still needs to know R1 behaves differently, even though every
other part of the system treats all models identically.

## System Prompt Design

The system prompt (in `brain.js`, versioned as `PROMPT_VERSION`, e.g.
`"v1.2"`, and persisted to a `prompt_versions` table on load) establishes:

1. **Identity, not just instructions:** *"You are not a task manager or
   chatbot. You are Tarun's guardian of context... Be sharp and direct,
   not polite and verbose."* — this framing measurably shapes tone, not
   just behavior.
2. **Explicit intent classification rules with examples**, including
   subtle cases like tense detection ("renewed X" = already done, past
   tense → `complete_todo`, never a new todo).
3. **A disambiguation rule that forbids silent guessing:** on genuinely
   50/50 messages (e.g. a bare noun like "gym"), the model must ask
   which was meant rather than guess — an explicit anti-hallucination
   guardrail baked into the prompt itself.
4. **Correction handling:** if the user says "no, I meant..." after a
   wrong action, the model is told to look at the *previous* user
   message (not its own prior reply) to resolve the reference.
5. **The proactive "Hermes layer" instructions** — explicit triggers for
   when to surface unprompted context (see
   [Proactive Features](./06-proactive-features.md)).
6. **Strict WhatsApp-appropriate formatting rules** — no markdown
   headers, no emoji, varied phrasing to avoid repetitive-sounding
   confirmations.
7. **A JSON-only output contract**, with both single-action and
   compound multi-action (`actions: [...]`) response shapes, and a
   `confidence` float on every response.
8. **Few-shot examples** covering the trickiest cases directly (the
   ambiguous-noun disambiguation, the correction-handling flow, compound
   requests) rather than relying on the rules alone.

**A second, narrower prompt** (`CLASSIFIER_PROMPT`) exists as a stripped-
down alternative — same JSON contract, but without the persona/tone
instructions, used presumably where classification-only accuracy
matters more than conversational quality (worth confirming exact call
site if extending this).

## Streaming Support

`callLLMStream` provides a separate code path for the mobile app's
real-time chat, using SSE-style chunked parsing
(`streamFromUrl` reads `data: ` lines, extracts
`.choices[0].delta.content` tokens) with the same Groq → OpenRouter
fallback ordering as the non-streaming path. Partial JSON is extracted
so the `reply` field can stream token-by-token to the UI before the full
structured response is complete.

## Embeddings

Embeddings use Gemini's `text-embedding-004` (768-dim) via the
`@google/generative-ai` SDK — a separate, single-provider call (no
fallback chain), used both to embed new notes/learnings/knowledge at
write time and to embed the current message for semantic memory search
at query time. See [Memory System](./04-memory-system.md) for how these
embeddings are queried.

## Common Mistakes to Avoid When Extending This

- **Adding a new model without adding it to `modelFailCache` cooldown
  logic** — an unhealthy model without cooldown tracking gets retried
  on every single request, adding latency to every failure instead of
  just the first one per 5-minute window.
- **Assuming all providers support the same request shape** — R1's
  `json_object` incompatibility is exactly this trap; any new model
  should be checked against the existing quirk-handling pattern before
  assuming it "just works" like the others.
- **Changing the JSON output schema without bumping `PROMPT_VERSION`** —
  the version is persisted specifically so past conversations/evals can
  be attributed to the prompt that generated them; silent schema drift
  defeats that.

## Related Docs

[Reliability](./07-reliability.md) covers the cooldown/fallback behavior
from a failure-handling lens. [Memory System](./04-memory-system.md)
covers how the context block fed into this prompt is assembled.
