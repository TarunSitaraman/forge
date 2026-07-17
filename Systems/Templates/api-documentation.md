# Template: API Documentation

*Guidance: API docs serve two different reader intents — "how do I
accomplish X" (a guide) and "what exactly does this endpoint do" (a
reference). Don't conflate them; a wall of exhaustive parameter tables is
a bad answer to "how do I get started," and a narrative guide is a bad
answer to "what are all the fields."*

---

# API: {API Name}

**Base URL:** `{base URL}` · **Version:** `{version}` · **Auth:** {method}

## Quick start

{The minimum request to get a real response — copy-pasteable, with a
real (or realistic sample) response shown.}

```bash
curl -X POST {base URL}/{endpoint} \
  -H "Authorization: Bearer {token}" \
  -d '{ "example": "payload" }'
```

```json
{ "example": "response" }
```

## Authentication

{How to obtain and use credentials. Link to a token/key management page
if separate.}

## Common tasks

{2-4 real usage guides for the most common things developers do with
this API — task-oriented, not endpoint-oriented.}

### {Task name, e.g. "Creating a resource and polling for completion"}
{Narrative walkthrough with code.}

## Endpoint reference

### `{METHOD} /{path}`

{One sentence: what this does.}

**Parameters**

| Name | Type | Required | Description |
|---|---|---|---|
| `{param}` | {type} | {yes/no} | {description} |

**Response**

```json
{ "field": "type and meaning" }
```

**Errors**

| Status | Code | Meaning |
|---|---|---|
| {4xx/5xx} | `{error_code}` | {when this happens} |

*(Repeat per endpoint.)*

## Rate limits

{Limits, how they're communicated (headers), and behavior on exceeding
them.}

## Versioning & deprecation policy

{How breaking changes are introduced, how long deprecated versions are
supported.}

## Changelog

{Link to a changelog, or a table of recent breaking/notable changes.}

---

## Best practices for this template
- Keep "Common tasks" and "Endpoint reference" clearly separate — one is
  for getting started fast, the other for exhaustive lookup.
- Every request example must be real/tested — a broken quick-start
  example undermines trust in the whole doc.
- Document error responses as thoroughly as success responses; consumers
  need to build real error handling, not just the happy path.
