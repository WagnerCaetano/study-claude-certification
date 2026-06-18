# Chapter 02: Building with the Claude API

> **Prerequisites:** Complete [Chapter 01: Claude 101](../01-claude-101/claude-101.md) first.

---

[⬅️ Back: Claude 101](../01-claude-101/claude-101.md) | [➡️ Next: Claude Code in Action](../03-claude-code-in-action/claude-code-in-action.md)

---

## 📋 Overview

This chapter dives deep into the Claude API — authentication, request/response formats, streaming, error handling, tool use, and best practices. This is heavily tested on the certification exam.

---

## 1. API Authentication

### 1.1 API Key Authentication

All API requests require an `x-api-key` header:

```python
# Python SDK handles authentication automatically
import anthropic

client = anthropic.Anthropic(api_key="sk-ant-api03-xxxxx")
# or via ANTHROPIC_API_KEY environment variable
```

```javascript
// Node.js SDK
const Anthropic = require('@anthropic-ai/sdk');

const client = new Anthropic({
    apiKey: process.env.ANTHROPIC_API_KEY
});
```

### 1.2 Direct HTTP Request

```bash
curl https://api.anthropic.com/v1/messages \
  -H "content-type: application/json" \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-20250514",
    "max_tokens": 1024,
    "messages": [
      {"role": "user", "content": "Hello, Claude!"}
    ]
  }'
```

### 1.3 Required Headers

| Header | Value | Description |
|--------|-------|-------------|
| `x-api-key` | `sk-ant-api03-...` | Your API key |
| `anthropic-version` | `2023-06-01` | API version |
| `content-type` | `application/json` | Request body format |

> 📝 **Exam Tip:** Know the required headers for direct API calls. The `anthropic-version` header is often forgotten.

---

## 2. Request Format

### 2.1 Basic Request Structure

```json
{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 1024,
  "system": "You are a helpful assistant.",
  "temperature": 0.7,
  "messages": [
    {"role": "user", "content": "Hello!"}
  ]
}
```

### 2.2 Required Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `model` | string | ✅ | Model identifier |
| `max_tokens` | integer | ✅ | Maximum output tokens |
| `messages` | array | ✅ | Conversation messages |

### 2.3 Optional Fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `system` | string | — | System prompt |
| `temperature` | float | 1.0 | Randomness (0.0-1.0) |
| `top_p` | float | — | Nucleus sampling |
| `top_k` | integer | — | Top-k sampling |
| `stop_sequences` | array | — | Stop generation triggers |
| `stream` | boolean | false | Enable streaming |
| `tools` | array | — | Available tools |
| `tool_choice` | object | — | Tool selection strategy |
| `metadata` | object | — | Request metadata |

---

## 3. Response Format

### 3.1 Standard Response

```json
{
  "id": "msg_01XFDUDYJgAACzvnptvVoYEL",
  "type": "message",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "Hello! How can I help you today?"
    }
  ],
  "model": "claude-sonnet-4-20250514",
  "stop_reason": "end_turn",
  "stop_sequence": null,
  "usage": {
    "input_tokens": 10,
    "output_tokens": 12
  }
}
```

### 3.2 Response Fields

| Field | Description |
|-------|-------------|
| `id` | Unique message identifier |
| `type` | Always `"message"` |
| `role` | Always `"assistant"` |
| `content` | Array of content blocks |
| `model` | Model used for generation |
| `stop_reason` | Why generation stopped |
| `usage` | Token usage statistics |

### 3.3 Stop Reasons

| Stop Reason | Meaning |
|-------------|---------|
| `end_turn` | Natural end of response |
| `max_tokens` | Hit token limit (response may be truncated) |
| `stop_sequence` | Hit a custom stop sequence |
| `tool_use` | Model wants to use a tool |

> 📝 **Exam Tip:** `max_tokens` stop reason means the response was **truncated**. You may need to continue the conversation or increase `max_tokens`.

---

## 4. Streaming

### 4.1 Enabling Streaming

```python
# Python — Streaming
with client.messages.stream(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Write a poem about coding."}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

```javascript
// Node.js — Streaming
const stream = client.messages.stream({
    model: "claude-sonnet-4-20250514",
    max_tokens: 1024,
    messages: [{ role: "user", content: "Write a poem about coding." }]
});

stream.on('text', (text) => {
    process.stdout.write(text);
});
```

### 4.2 Streaming Events

When streaming, you receive events:

| Event | Description |
|-------|-------------|
| `message_start` | Message metadata (model, usage) |
| `content_block_start` | New content block begins |
| `content_block_delta` | Incremental text/tool use data |
| `content_block_stop` | Content block ends |
| `message_delta` | Updated stop reason, usage |
| `message_stop` | Stream complete |

### 4.3 When to Use Streaming

| Scenario | Use Streaming? |
|----------|---------------|
| Chat interface | ✅ Yes — Better UX |
| Batch processing | ❌ No — Collect full response |
| API service | ❌ No — Process complete response |
| Real-time assistant | ✅ Yes — Immediate feedback |

---

## 5. Tool Use (Function Calling)

### 5.1 Defining Tools

```python
tools = [
    {
        "name": "get_weather",
        "description": "Get the current weather for a location",
        "input_schema": {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "The city and state, e.g. San Francisco, CA"
                },
                "unit": {
                    "type": "string",
                    "enum": ["celsius", "fahrenheit"],
                    "description": "Temperature unit"
                }
            },
            "required": ["location"]
        }
    },
    {
        "name": "search_database",
        "description": "Search a database for information",
        "input_schema": {
            "type": "object",
            "properties": {
                "query": {"type": "string"},
                "limit": {"type": "integer", "default": 10}
            },
            "required": ["query"]
        }
    }
]
```

### 5.2 Complete Tool Use Flow

```python
import anthropic
import json

client = anthropic.Anthropic()

# Step 1: Send message with tools
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    tools=tools,
    messages=[{
        "role": "user",
        "content": "What's the weather in San Francisco and New York?"
    }]
)

# Step 2: Check if Claude wants to use a tool
if response.stop_reason == "tool_use":
    for content_block in response.content:
        if content_block.type == "tool_use":
            # Step 3: Execute the tool
            tool_result = execute_tool(
                content_block.name,
                content_block.input
            )
            
            # Step 4: Send tool result back to Claude
            response = client.messages.create(
                model="claude-sonnet-4-20250514",
                max_tokens=1024,
                tools=tools,
                messages=[
                    {"role": "user", "content": "What's the weather in SF and NYC?"},
                    {"role": "assistant", "content": response.content},
                    {
                        "role": "user",
                        "content": [{
                            "type": "tool_result",
                            "tool_use_id": content_block.id,
                            "content": json.dumps(tool_result)
                        }]
                    }
                ]
            )

print(response.content[0].text)
```

### 5.3 Tool Choice Options

```python
# Auto (default) — Claude decides when to use tools
tool_choice = {"type": "auto"}

# Any tool — Claude must use one of the provided tools
tool_choice = {"type": "any"}

# Specific tool — Claude must use a specific tool
tool_choice = {"type": "tool", "name": "get_weather"}

# Disable tool use for this request
tool_choice = {"type": "none"}  # Not always available; omit tools instead
```

> 📝 **Exam Tip:** Understand the tool use flow: **define tools → Claude decides to use tool → you execute tool → send result back → Claude continues**.

---

## 6. Error Handling

### 6.1 Common Error Types

```python
import anthropic

try:
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=1024,
        messages=[{"role": "user", "content": "Hello!"}]
    )
except anthropic.AuthenticationError as e:
    print(f"Authentication failed: {e}")
except anthropic.RateLimitError as e:
    print(f"Rate limited: {e}")
    # Implement backoff and retry
except anthropic.BadRequestError as e:
    print(f"Bad request: {e}")
except anthropic.APIConnectionError as e:
    print(f"Connection error: {e}")
except anthropic.APIStatusError as e:
    print(f"API error {e.status_code}: {e.message}")
```

### 6.2 Error Codes

| HTTP Code | Error | Meaning |
|-----------|-------|---------|
| 400 | `invalid_request_error` | Malformed request |
| 401 | `authentication_error` | Invalid API key |
| 403 | `permission_error` | Insufficient permissions |
| 404 | `not_found_error` | Resource not found |
| 429 | `rate_limit_error` | Too many requests |
| 500 | `api_error` | Internal server error |
| 529 | `overloaded_error` | API is overloaded |

### 6.3 Retry Strategy

```python
import anthropic
import time

def call_with_retry(client, max_retries=3, **kwargs):
    """Call the API with exponential backoff."""
    for attempt in range(max_retries):
        try:
            return client.messages.create(**kwargs)
        except anthropic.RateLimitError:
            wait_time = 2 ** attempt
            print(f"Rate limited. Waiting {wait_time}s...")
            time.sleep(wait_time)
        except anthropic.APIConnectionError:
            wait_time = 2 ** attempt
            print(f"Connection error. Retrying in {wait_time}s...")
            time.sleep(wait_time)
    
    raise Exception(f"Failed after {max_retries} retries")
```

---

## 7. Advanced Features

### 7.1 Vision (Image Analysis)

```python
import base64

# Read and encode an image
with open("chart.png", "rb") as f:
    image_data = base64.standard_b64encode(f.read()).decode("utf-8")

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": [
            {
                "type": "image",
                "source": {
                    "type": "base64",
                    "media_type": "image/png",
                    "data": image_data
                }
            },
            {
                "type": "text",
                "text": "What insights can you draw from this chart?"
            }
        ]
    }]
)
```

### 7.2 PDF Support

```python
# Analyze a PDF document
with open("report.pdf", "rb") as f:
    pdf_data = base64.standard_b64encode(f.read()).decode("utf-8")

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=4096,
    messages=[{
        "role": "user",
        "content": [
            {
                "type": "document",
                "source": {
                    "type": "base64",
                    "media_type": "application/pdf",
                    "data": pdf_data
                }
            },
            {
                "type": "text",
                "text": "Summarize the key findings of this report."
            }
        ]
    }]
)
```

### 7.3 JSON Mode

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": """Extract the entities from this text and return as JSON.
        
Text: "Apple Inc. was founded by Steve Jobs in Cupertino, California."

Return format: {"entities": [{"name": string, "type": string, "details": string}]}"""
    }]
)
```

### 7.4 Prompt Caching

Prompt caching reduces costs by caching frequently used prompts:

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    system=[
        {
            "type": "text",
            "text": "You are an AI assistant with access to a large knowledge base...",
            "cache_control": {"type": "ephemeral"}
        }
    ],
    messages=[{
        "role": "user",
        "content": "What are the key principles of AI safety?"
    }]
)
```

> 📝 **Exam Tip:** Prompt caching can reduce costs by up to 90% for repeated system prompts. Mark content with `cache_control: {"type": "ephemeral"}`.

---

## 8. Best Practices

### 8.1 Cost Optimization

| Strategy | Impact |
|----------|--------|
| Use the right model for the task | High |
| Set appropriate `max_tokens` | Medium |
| Use prompt caching | High (for repeated prompts) |
| Batch similar requests | Medium |
| Use Haiku for simple tasks | High |

### 8.2 Performance Tips

1. **Minimize context** — Don't send unnecessary history
2. **Use streaming** for responsive UX
3. **Implement caching** for repeated queries
4. **Parallelize** independent requests
5. **Use system prompts** to set behavior once

### 8.3 Security Best Practices

```python
# ✅ DO: Use environment variables
client = anthropic.Anthropic()  # Uses ANTHROPIC_API_KEY env var

# ❌ DON'T: Hardcode API keys
client = anthropic.Anthropic(api_key="sk-ant-api03-xxxxx")  # Never do this!

# ✅ DO: Validate and sanitize user input before sending to Claude
user_input = sanitize_input(request.form.get("message"))

# ❌ DON'T: Trust Claude's output blindly for security-critical operations
# Always validate code, SQL, and commands before execution
```

---

## 🧪 Mini-Quiz: Building with the API

**Q1:** What HTTP status code indicates rate limiting?

<details>
<summary>Show Answer</summary>

**429** — Rate limit error. Implement exponential backoff and retry.
</details>

**Q2:** How do you force Claude to use a specific tool?

<details>
<summary>Show Answer</summary>

Use `tool_choice: {"type": "tool", "name": "tool_name"}` in your request.
</details>

**Q3:** What is the correct flow for tool use?

<details>
<summary>Show Answer</summary>

1. Send message with tool definitions
2. Claude responds with `tool_use` content block
3. You execute the tool locally
4. Send the tool result back as a `tool_result` content block
5. Claude generates a final response incorporating the tool result
</details>

---

## 🔑 Key Takeaways

1. **Authentication** uses `x-api-key` header and `anthropic-version` header
2. **Required fields**: `model`, `max_tokens`, `messages`
3. **Streaming** provides incremental responses via SSE events
4. **Tool use** follows a request-execute-return pattern
5. **Error handling** should include retry logic with exponential backoff
6. **Prompt caching** reduces costs for repeated system prompts
7. **Security**: Never hardcode keys, always validate input/output

---

[⬅️ Back: Claude 101](../01-claude-101/claude-101.md) | [➡️ Next: Claude Code in Action](../03-claude-code-in-action/claude-code-in-action.md)
