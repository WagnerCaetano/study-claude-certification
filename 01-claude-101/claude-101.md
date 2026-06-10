# Chapter 01: Claude 101 – Core Concepts

> **Prerequisites:** Complete [Chapter 00: Setup](../00-setup/setting-up-your-machine.md) first.

---

[⬅️ Back: Setup](../00-setup/setting-up-your-machine.md) | [➡️ Next: Building with the API](../02-building-with-api/building-with-claude-api.md)

---

## 📋 Overview

This chapter covers the fundamental concepts of Claude AI — what it is, how it works, its capabilities, limitations, and how to interact with it effectively. This is **foundational knowledge** for the certification exam.

---

## 1. What is Claude?

### 1.1 Definition

Claude is a family of large language models (LLMs) developed by **Anthropic**. It is designed to be:

- **Helpful** — Provides useful, relevant responses
- **Harmless** — Avoids generating harmful content
- **Honest** — Acknowledges uncertainty and avoids hallucination

### 1.2 The Claude Model Family

| Model | Strengths | Best For |
|-------|-----------|----------|
| **Claude Opus 4** | Most capable, best reasoning | Complex tasks, research, advanced coding |
| **Claude Sonnet 4** | Balanced performance/cost | General tasks, most API use cases |
| **Claude Haiku 3.5** | Fastest, most cost-effective | Simple tasks, high-volume processing |

### 1.3 Model Naming Convention

Anthropic uses a specific naming pattern:

```
claude-{family}-{version}-{date}

Examples:
claude-sonnet-4-20250514
claude-opus-4-20250514
claude-3-5-haiku-20241022
```

> 📝 **Exam Tip:** Know the difference between model families and when to use each. Questions often ask you to select the best model for a given scenario.

---

## 2. How Claude Works

### 2.1 The Transformer Architecture

Claude is built on the **transformer architecture**, which processes text using:

1. **Tokenization** — Text is split into tokens (words/subwords)
2. **Embedding** — Tokens are converted to numerical vectors
3. **Attention** — The model learns relationships between tokens
4. **Generation** — Predicts the next token probabilistically

### 2.2 Context Windows

The **context window** is the maximum amount of text Claude can process in a single request:

| Model | Context Window |
|-------|---------------|
| Claude Opus 4 | 200K tokens |
| Claude Sonnet 4 | 200K tokens |
| Claude Haiku 3.5 | 200K tokens |

> 💡 **Key Concept:** 200K tokens ≈ 150,000 words ≈ 500 pages of text

### 2.3 Token Economics

```
Token Counting Rules of Thumb:
- 1 token ≈ 4 characters (English)
- 1 token ≈ ¾ of a word
- "Hello, world!" ≈ 3 tokens
- Code is typically more tokens than natural language
```

---

## 3. Interacting with Claude

### 3.1 The Messages API

Claude uses a **conversation-based API** organized around **messages**:

```python
import anthropic

client = anthropic.Anthropic()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Hello, Claude!"}
    ]
)
```

### 3.2 Conversation Roles

| Role | Description |
|------|-------------|
| `user` | The human/user message |
| `assistant` | Claude's response |

### 3.3 Multi-turn Conversations

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "What is Python?"},
        {"role": "assistant", "content": "Python is a high-level programming language..."},
        {"role": "user", "content": "What are its main advantages?"}
    ]
)
```

> ⚠️ **Important:** The conversation history is sent with each request. Claude doesn't "remember" previous API calls — you must include the full conversation each time.

### 3.4 The System Prompt

The **system prompt** sets Claude's behavior for the entire conversation:

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    system="You are a helpful math tutor. Always show your work step by step.",
    messages=[
        {"role": "user", "content": "What is 15% of 240?"}
    ]
)
```

**System Prompt Best Practices:**

1. **Be specific** about the role and behavior
2. **Set the output format** (JSON, markdown, etc.)
3. **Define constraints** (length, tone, language)
4. **Provide examples** of expected behavior
5. **Use XML tags** for structured instructions

```python
system_prompt = """
You are a data extraction assistant. Your task is to extract 
information from resumes and return it in JSON format.

<rules>
- Extract: name, email, phone, skills, experience
- If a field is missing, use null
- Skills should be a list of strings
- Experience should be a list of objects with company, title, years
</rules>

<output_format>
Return valid JSON only. No markdown formatting.
</output_format>
"""
```

---

## 4. Key Parameters

### 4.1 `max_tokens`

Controls the **maximum length** of Claude's response:

```python
# Short response
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=50,
    messages=[{"role": "user", "content": "What is 2+2?"}]
)

# Long response
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=4096,
    messages=[{"role": "user", "content": "Write a detailed essay on AI safety."}]
)
```

> 📝 **Exam Tip:** `max_tokens` is **required** — you must always specify it. Setting it too low will truncate responses.

### 4.2 `temperature`

Controls **randomness** in responses (0.0 to 1.0):

```python
# Deterministic, focused output (good for code, factual answers)
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    temperature=0.0,
    messages=[{"role": "user", "content": "Extract the date from this text..."}]
)

# Creative, varied output (good for brainstorming, writing)
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    temperature=0.7,
    messages=[{"role": "user", "content": "Write a poem about programming."}]
)
```

| Temperature | Use Case |
|-------------|----------|
| 0.0 | Code generation, data extraction, classification |
| 0.3 | Summarization, translation |
| 0.7 | Creative writing, brainstorming |
| 1.0 | Maximum creativity, exploration |

### 4.3 `top_p`, `top_k`

Alternative sampling parameters:

- **`top_p`** (nucleus sampling): Limits to tokens within cumulative probability p
- **`top_k`**: Limits to top k most probable tokens

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    top_p=0.9,
    top_k=50,
    messages=[{"role": "user", "content": "Explain quantum computing."}]
)
```

> 💡 **Tip:** Anthropic recommends adjusting `temperature` first. Only use `top_p`/`top_k` for fine-grained control.

### 4.4 `stop_sequences`

Stop generation when specific strings are encountered:

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    stop_sequences=["\n\nHuman:", "\n\nQuestion:"],
    messages=[{"role": "user", "content": "List 10 facts about space."}]
)
```

---

## 5. Claude's Capabilities

### 5.1 Text Generation & Analysis

- **Summarization** — Condense long documents
- **Translation** — Between natural languages
- **Rewriting** — Change tone, style, or format
- **Analysis** — Extract insights from text

### 5.2 Code Generation & Understanding

```python
# Claude can write, explain, debug, and refactor code
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=2048,
    system="You are an expert Python developer. Write clean, well-documented code.",
    messages=[{
        "role": "user", 
        "content": "Write a function that finds the longest palindromic substring in a string."
    }]
)
```

### 5.3 Vision (Multimodal)

Claude can analyze images:

```python
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
                    "data": "<base64_encoded_image>"
                }
            },
            {
                "type": "text",
                "text": "What's in this image? Describe it in detail."
            }
        ]
    }]
)
```

### 5.4 Tool Use (Function Calling)

Claude can use external tools via structured outputs:

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    tools=[{
        "name": "get_weather",
        "description": "Get the current weather in a location",
        "input_schema": {
            "type": "object",
            "properties": {
                "location": {"type": "string", "description": "City name"},
                "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]}
            },
            "required": ["location"]
        }
    }],
    messages=[{
        "role": "user",
        "content": "What's the weather in Tokyo?"
    }]
)
```

---

## 6. Prompt Engineering Fundamentals

### 6.1 Be Clear and Specific

```
❌ Bad: "Tell me about Python"
✅ Good: "Explain Python's list comprehension syntax with 3 practical examples"
```

### 6.2 Use XML Tags for Structure

```python
prompt = """
Please analyze the following document and answer the question.

<document>
{document_text}
</document>

<question>
What are the main arguments presented?
</question>

<instructions>
- List each argument as a separate bullet point
- Include supporting quotes from the document
- Rate the strength of each argument (weak/medium/strong)
</instructions>
"""
```

### 6.3 Provide Examples (Few-Shot)

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": """
Classify the sentiment of each review.

<examples>
Review: "This product is amazing!" → Positive
Review: "Terrible experience, would not recommend." → Negative
Review: "It's okay, nothing special." → Neutral
</examples>

<review>
"The food was great but the service was slow."
</review>
"""
    }]
)
```

### 6.4 Chain of Thought Prompting

```python
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=2048,
    messages=[{
        "role": "user",
        "content": """
Solve this step by step.

A store has a 20% off sale. An item originally costs $85. 
A customer has a $10 coupon that applies after the sale discount.
There's also 8% sales tax.
What is the final price?
"""
    }]
)
```

### 6.5 Assign a Role

```python
system = """You are a senior security engineer with 15 years of experience 
in application security. You specialize in:
- OWASP Top 10 vulnerabilities
- Secure code review
- Threat modeling
- Penetration testing

Always provide specific, actionable advice with code examples."""
```

---

## 7. Limitations and Safety

### 7.1 Known Limitations

| Limitation | Description | Mitigation |
|------------|-------------|------------|
| **No internet access** | Cannot browse the web in real-time | Use tools/APIs to fetch data |
| **Knowledge cutoff** | Training data has a cutoff date | Provide recent information in context |
| **Hallucination** | May generate plausible but incorrect info | Verify facts, use grounded prompts |
| **Context limit** | Cannot process beyond context window | Chunk large documents, use RAG |
| **No persistent memory** | Each request is stateless | Manage conversation history yourself |

### 7.2 Constitutional AI

Claude is trained using **Constitutional AI (CAI)**, which:

1. Defines a set of principles (a "constitution")
2. Trains the model to follow these principles
3. Enables self-correction during training
4. Reduces harmful outputs without extensive human labeling

### 7.3 Safety Features

- **Content filtering** — Refuses harmful requests
- **Honesty** — Will express uncertainty
- **Privacy** — Won't reproduce personal information from training data

---

## 8. The Anthropic API Ecosystem

### 8.1 Key Platforms

| Platform | Purpose |
|----------|---------|
| **claude.ai** | Consumer chatbot interface |
| **Console** | API key management, usage tracking |
| **Claude Code** | CLI tool for developer workflows |
| **Claude Desktop** | Desktop application with MCP support |
| **API** | Programmatic access to Claude |

### 8.2 Pricing Model

- **Input tokens** — Tokens in your prompt (cheaper)
- **Output tokens** — Tokens in Claude's response (more expensive)

```python
# Track token usage
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Hello!"}]
)

print(f"Input tokens: {response.usage.input_tokens}")
print(f"Output tokens: {response.usage.output_tokens}")
```

---

## 🧪 Mini-Quiz: Claude 101

**Q1:** Which model would you use for a high-volume, low-latency classification task?

<details>
<summary>Show Answer</summary>

**Claude Haiku 3.5** — It's optimized for speed and cost-efficiency, making it ideal for high-volume tasks like classification.
</details>

**Q2:** What happens if you don't include `max_tokens` in your API request?

<details>
<summary>Show Answer</summary>

The request will fail. `max_tokens` is a **required** parameter in the Messages API.
</details>

**Q3:** True or False: Claude remembers your previous API calls automatically.

<details>
<summary>Show Answer</summary>

**False.** Claude's API is stateless. You must include the full conversation history in each request.
</details>

**Q4:** What is the context window size for Claude Sonnet 4?

<details>
<summary>Show Answer</summary>

**200K tokens** — All current Claude 4 models support a 200K token context window.
</details>

---

## 🔑 Key Takeaways

1. **Claude** is Anthropic's family of LLMs with three tiers: Opus (most capable), Sonnet (balanced), Haiku (fastest)
2. **Messages API** uses a conversation format with `user` and `assistant` roles
3. **System prompts** control Claude's behavior and are set per-conversation
4. **Key parameters**: `max_tokens` (required), `temperature`, `top_p`, `top_k`
5. **Prompt engineering** is crucial — be specific, use structure (XML tags), provide examples
6. **Safety** is built-in through Constitutional AI training

---

[⬅️ Back: Setup](../00-setup/setting-up-your-machine.md) | [➡️ Next: Building with the API](../02-building-with-api/building-with-claude-api.md)
