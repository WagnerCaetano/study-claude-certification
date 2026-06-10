# 📝 Practice Exam 1: Claude Certified Architect

> **Time Limit:** 90 minutes | **Questions:** 50 | **Passing Score:** 80%

---

[⬅️ Back to Index](../README.md) | [➡️ Next: Practice Exam 2](./practice-exam-2.md)

---

## Instructions

- Select the **best** answer for each question
- Some questions may have multiple correct answers (indicated by "Select all that apply")
- You have 90 minutes to complete all 50 questions

---

## Section A: Claude Fundamentals (Questions 1-10)

### Question 1

**Which Claude model family is best suited for high-volume, low-latency tasks like text classification?**

A) Claude Opus 4  
B) Claude Sonnet 4  
C) Claude Haiku 3.5  
D) All models perform equally  

<details>
<summary>✅ Answer</summary>

**C) Claude Haiku 3.5**

Claude Haiku is optimized for speed and cost-efficiency, making it ideal for high-volume tasks like classification where low latency is critical.
</details>

---

### Question 2

**What is the context window size for Claude Sonnet 4?**

A) 8K tokens  
B) 32K tokens  
C) 100K tokens  
D) 200K tokens  

<details>
<summary>✅ Answer</summary>

**D) 200K tokens**

All current Claude 4 models (Opus, Sonnet) and Claude 3.5 models support a 200K token context window.
</details>

---

### Question 3

**Which parameter is REQUIRED when making a request to the Claude Messages API?**

A) temperature  
B) system  
C) max_tokens  
D) top_p  

<details>
<summary>✅ Answer</summary>

**C) max_tokens**

`max_tokens` is a required parameter. The API will return an error if it's not specified. The `temperature`, `system`, and `top_p` parameters are optional.
</details>

---

### Question 4

**What happens if you set `max_tokens` too low for a response?**

A) The API returns an error  
B) The response is truncated  
C) Claude automatically adjusts the value  
D) The request is queued for retry  

<details>
<summary>✅ Answer</summary>

**B) The response is truncated**

If `max_tokens` is set too low, Claude's response will be cut off. You can check the `stop_reason` field — it will be `"max_tokens"` instead of `"end_turn"`.
</details>

---

### Question 5

**Which of the following is NOT a valid message role in the Claude API?**

A) user  
B) assistant  
C) system  
D) tool  

<details>
<summary>✅ Answer</summary>

**C) system**

The `system` parameter is separate from the messages array. Valid roles within messages are `user` and `assistant`. Tool results use a special content block type within a `user` message.
</details>

---

### Question 6

**What does a `temperature` of 0.0 produce?**

A) Maximum creativity  
B) Random, varied outputs  
C) The most deterministic, focused output  
D) An error — temperature cannot be 0  

<details>
<summary>✅ Answer</summary>

**C) The most deterministic, focused output**

A temperature of 0.0 produces the most deterministic output, selecting the most probable token each time. This is ideal for code generation, data extraction, and factual tasks.
</details>

---

### Question 7

**How does Claude handle multi-turn conversations?**

A) Claude remembers all previous API calls automatically  
B) You must include the full conversation history in each request  
C) Claude uses cookies to maintain sessions  
D) You need to enable session mode in the API  

<details>
<summary>✅ Answer</summary>

**B) You must include the full conversation history in each request**

The Claude API is stateless. You must send the complete conversation history (all user and assistant messages) with every new request.
</details>

---

### Question 8

**What is Constitutional AI (CAI)?**

A) A legal compliance framework for AI  
B) A training method where AI follows a set of principles  
C) A government regulation for AI development  
D) A prompt engineering technique  

<details>
<summary>✅ Answer</summary>

**B) A training method where AI follows a set of principles**

Constitutional AI is Anthropic's approach to training AI systems to be helpful, harmless, and honest by using a set of principles (a "constitution") that guides the model's behavior.
</details>

---

### Question 9

**Approximately how many words does 1 token represent?**

A) 1 word  
B) 3/4 of a word  
C) 1/4 of a word  
D) 2 words  

<details>
<summary>✅ Answer</summary>

**B) 3/4 of a word**

As a rule of thumb, 1 token ≈ 3/4 of a word, or about 4 characters in English text.
</details>

---

### Question 10

**Which stop_reason value indicates Claude completed its response naturally?**

A) `max_tokens`  
B) `stop_sequence`  
C) `end_turn`  
D) `complete`  

<details>
<summary>✅ Answer</summary>

**C) `end_turn`**

`end_turn` means Claude finished generating its response naturally. `max_tokens` means it was truncated, and `stop_sequence` means a stop sequence was triggered.
</details>

---

## Section B: API Development (Questions 11-20)

### Question 11

**What is the correct way to authenticate with the Anthropic API?**

A) Send username and password in the request body  
B) Pass an API key via the `x-api-key` header  
C) Use OAuth 2.0 with client credentials  
D) Include a JWT token in the URL  

<details>
<summary>✅ Answer</summary>

**B) Pass an API key via the `x-api-key` header**

The Anthropic API uses API keys for authentication, passed via the `x-api-key` HTTP header. The SDK handles this automatically when you set the `ANTHROPIC_API_KEY` environment variable.
</details>

---

### Question 12

**What does the streaming API provide?**

A) Faster total response time  
B) Real-time token-by-token delivery  
C) Smaller response sizes  
D) Higher quality responses  

<details>
<summary>✅ Answer</summary>

**B) Real-time token-by-token delivery**

Streaming allows you to receive tokens as they are generated, improving perceived latency. The total response time is similar, but users see output sooner.
</details>

---

### Question 13

**When using tool use, what does `stop_reason: "tool_use"` indicate?**

A) The tool encountered an error  
B) Claude wants to call a tool before responding  
C) The tool has finished executing  
D) No tools are available  

<details>
<summary>✅ Answer</summary>

**B) Claude wants to call a tool before responding**

When `stop_reason` is `"tool_use"`, Claude has decided it needs to invoke one of the provided tools. You should extract the tool call from the response, execute it, and return the result.
</details>

---

### Question 14

**Which error type should NOT be retried automatically?**

A) RateLimitError  
B) APIConnectionError  
C) BadRequestError  
D) APIStatusError with status 500  

<details>
<summary>✅ Answer</summary>

**C) BadRequestError**

A `BadRequestError` (400) indicates a problem with your request itself — fixing and retrying won't help without changing the request. Rate limits and connection errors are transient and can be retried.
</details>

---

### Question 15

**How do you implement prompt caching effectively?**

A) Set `cache_control: {"type": "ephemeral"}` on stable, large content  
B) Add `cache=true` to the URL parameters  
C) Enable caching in the API dashboard  
D) Use a special `cache` parameter in the request  

<details>
<summary>✅ Answer</summary>

**A) Set `cache_control: {"type": "ephemeral"}` on stable, large content**

Prompt caching is implemented by adding the `cache_control` header to specific content blocks, marking them as cacheable. This works best with large, static content like system prompts or reference documents.
</details>

---

### Question 16

**What is the recommended way to handle API rate limits?**

A) Send requests as fast as possible  
B) Implement exponential backoff  
C) Use multiple API keys  
D) Increase the timeout value  

<details>
<summary>✅ Answer</summary>

**B) Implement exponential backoff**

Exponential backoff (waiting 2^n seconds between retries) is the recommended approach for handling rate limits. It progressively increases wait time, allowing the API to recover.
</details>

---

### Question 17

**In the tool use flow, after receiving a tool result, what role should the message have?**

A) `assistant`  
B) `tool`  
C) `user`  
D) `system`  

<details>
<summary>✅ Answer</summary>

**C) `user`**

Tool results are sent back as `user` messages with a `tool_result` content block type. This follows the conversation pattern where the human (or human's system) provides the tool output.
</details>

---

### Question 18

**What is the purpose of the `anthropic-version` header?**

A) To specify which Claude model to use  
B) To indicate the API version being used  
C) To check the SDK version  
D) To enable beta features  

<details>
<summary>✅ Answer</summary>

**B) To indicate the API version being used**

The `anthropic-version` header specifies which version of the API you're using (e.g., `2023-06-01`). This ensures backward compatibility as the API evolves.
</details>

---

### Question 19

**Which is the most cost-effective approach for processing 10,000 short documents?**

A) Use Claude Opus with batch processing  
B) Use Claude Haiku with concurrent requests  
C) Use Claude Sonnet with streaming  
D) Use Claude Opus with prompt caching  

<details>
<summary>✅ Answer</summary>

**B) Use Claude Haiku with concurrent requests**

For high-volume, short document processing, Claude Haiku provides the best cost-efficiency and speed. Concurrent requests maximize throughput.
</details>

---

### Question 20

**What does prompt caching reduce?**

A) Output token costs  
B) Input token costs for repeated content  
C) The number of API errors  
D) Response latency for all requests  

<details>
<summary>✅ Answer</summary>

**B) Input token costs for repeated content**

Prompt caching reduces the cost of input tokens when the same content is sent across multiple requests. Cached tokens are significantly cheaper than regular input tokens.
</details>

---

## Section C: Claude Code & Agent Skills (Questions 21-30)

### Question 21

**How do you install Claude Code CLI?**

A) `pip install claude-code`  
B) `npm install -g @anthropic-ai/claude-code`  
C) `brew install claude`  
D) Download from console.anthropic.com  

<details>
<summary>✅ Answer</summary>

**B) `npm install -g @anthropic-ai/claude-code`**

Claude Code is installed globally via npm using `npm install -g @anthropic-ai/claude-code`.
</details>

---

### Question 22

**What is the primary purpose of Claude Code?**

A) A code editor for writing Claude applications  
B) An agentic CLI tool for developer workflows  
C) A library for training Claude models  
D) A GUI for the Anthropic console  

<details>
<summary>✅ Answer</summary>

**B) An agentic CLI tool for developer workflows**

Claude Code is an agentic CLI tool that helps developers with coding tasks including writing, debugging, and managing code projects using natural language commands.
</details>

---

### Question 23

**Which agent pattern involves thinking before acting?**

A) Direct execution  
B) ReAct (Reason + Act)  
C) Map-Reduce  
D) Pipeline  

<details>
<summary>✅ Answer</summary>

**B) ReAct (Reason + Act)**

The ReAct pattern alternates between reasoning (thinking about what to do) and acting (executing a tool), producing more reliable and interpretable agent behavior.
</details>

---

### Question 24

**What is a key safety measure when building agents?**

A) Give agents unlimited iterations  
B) Set maximum iteration limits  
C) Allow agents to execute any system command  
D) Disable all error handling  

<details>
<summary>✅ Answer</summary>

**B) Set maximum iteration limits**

Always set a maximum iteration limit for agent loops to prevent infinite loops and unbounded API costs.
</details>

---

### Question 25

**In Claude Code, what does `CLAUDE.md` do?**

A) Stores API keys  
B) Provides project-specific instructions to Claude  
C) Tracks usage metrics  
D) Manages npm packages  

<details>
<summary>✅ Answer</summary>

**B) Provides project-specific instructions to Claude**

`CLAUDE.md` is a project-level configuration file that gives Claude Code context about your project, coding conventions, and preferred practices.
</details>

---

### Question 26

**What is guardrails in agent design?**

A) Physical safety barriers  
B) Rules and constraints that limit agent behavior  
C) A specific Anthropic product  
D) Performance monitoring tools  

<details>
<summary>✅ Answer</summary>

**B) Rules and constraints that limit agent behavior**

Guardrails are safety constraints built into agent systems to prevent unwanted behaviors, such as validating tool inputs, limiting actions, and requiring human approval for sensitive operations.
</details>

---

### Question 27

**Which is a characteristic of the Plan-and-Execute agent pattern?**

A) It plans all steps upfront, then executes them  
B) It acts randomly without planning  
C) It only uses one tool  
D) It cannot handle multi-step tasks  

<details>
<summary>✅ Answer</summary>

**A) It plans all steps upfront, then executes them**

The Plan-and-Execute pattern first generates a complete plan of action, then executes each step sequentially. This provides better control and predictability compared to reactive patterns.
</details>

---

### Question 28

**What is multi-agent orchestration?**

A) Running multiple Claude instances on one machine  
B) Coordinating multiple specialized agents to complete a task  
C) Training multiple models simultaneously  
D) Load balancing API requests  

<details>
<summary>✅ Answer</summary>

**B) Coordinating multiple specialized agents to complete a task**

Multi-agent orchestration involves multiple agents, each with specialized roles, working together under a coordinator to accomplish complex tasks.
</details>

---

### Question 29

**Which is the best practice for agent tool design?**

A) Create one large, complex tool that does everything  
B) Create focused, single-purpose tools with clear descriptions  
C) Tools don't need descriptions  
D) Use only built-in tools  

<details>
<summary>✅ Answer</summary>

**B) Create focused, single-purpose tools with clear descriptions**

Well-designed tools are focused on a single purpose with clear, specific descriptions. This helps Claude choose the right tool and use it correctly.
</details>

---

### Question 30

**What is the purpose of a human-in-the-loop pattern?**

A) To eliminate automation entirely  
B) To require human approval for critical agent actions  
C) To slow down the agent  
D) To train the model  

<details>
<summary>✅ Answer</summary>

**B) To require human approval for critical agent actions**

Human-in-the-loop patterns ensure that important or sensitive agent actions are reviewed and approved by a human before execution, adding a safety layer.
</details>

---

## Section D: MCP (Model Context Protocol) (Questions 31-40)

### Question 31

**What is the Model Context Protocol (MCP)?**

A) A proprietary Anthropic protocol  
B) An open protocol for connecting AI models to tools and data  
C) A machine learning training protocol  
D) A communication protocol between Claude models  

<details>
<summary>✅ Answer</summary>

**B) An open protocol for connecting AI models to tools and data**

MCP is an open protocol that standardizes how AI models interact with external tools, data sources, and services. It's not limited to Claude — any AI model can use it.
</details>

---

### Question 32

**Which are the three main primitives in MCP? (Select all that apply)**

A) Tools  
B) Resources  
C) Prompts  
D) Models  
E) Agents  

<details>
<summary>✅ Answer</summary>

**A) Tools, B) Resources, C) Prompts**

MCP's three main primitives are Tools (actions the AI can perform), Resources (data the AI can read), and Prompts (templates the AI can use).
</details>

---

### Question 33

**What transport mechanism does MCP use for local communication?**

A) HTTP/REST  
B) WebSocket  
C) Standard I/O (stdio)  
D) gRPC  

<details>
<summary>✅ Answer</summary>

**C) Standard I/O (stdio)**

MCP uses stdio (standard input/output) for local communication between the client and server. For remote communication, it uses HTTP with SSE (Server-Sent Events).
</details>

---

### Question 34

**What is an MCP server?**

A) A physical server running Claude  
B) A program that exposes tools, resources, and prompts via MCP  
C) The Anthropic API server  
D) A database server for model training  

<details>
<summary>✅ Answer</summary>

**B) A program that exposes tools, resources, and prompts via MCP**

An MCP server is a lightweight program that implements the MCP protocol, exposing capabilities (tools, resources, prompts) that AI models can use.
</details>

---

### Question 35

**How do you configure MCP servers in Claude Desktop?**

A) Through the command line  
B) By editing the `claude_desktop_config.json` file  
C) Through the Anthropic console  
D) By installing npm packages globally  

<details>
<summary>✅ Answer</summary>

**B) By editing the `claude_desktop_config.json` file**

MCP servers are configured in the Claude Desktop configuration file, typically located at `~/.claude/claude_desktop_config.json` (or the Windows equivalent).
</details>

---

### Question 36

**What is the difference between MCP Tools and MCP Resources?**

A) Tools are for reading data; Resources are for actions  
B) Tools are for actions/side effects; Resources are for reading data  
C) There is no difference  
D) Tools are for images; Resources are for text  

<details>
<summary>✅ Answer</summary>

**B) Tools are for actions/side effects; Resources are for reading data**

MCP Tools represent actions the model can take (with potential side effects), while Resources represent data sources the model can read (without side effects).
</details>

---

### Question 37

**Which SDK is commonly used to build MCP servers in TypeScript?**

A) `@anthropic-ai/sdk`  
B) `@modelcontextprotocol/sdk`  
C) `express`  
D) `fastify`  

<details>
<summary>✅ Answer</summary>

**B) `@modelcontextprotocol/sdk`**

The official MCP SDK for TypeScript is `@modelcontextprotocol/sdk`. It provides the `McpServer` class and transport implementations.
</details>

---

### Question 38

**What is MCP sampling?**

A) Collecting data samples from APIs  
B) Allowing MCP servers to make LLM requests through the client  
C) A technique for reducing token usage  
D) A method for testing MCP servers  

<details>
<summary>✅ Answer</summary>

**B) Allowing MCP servers to make LLM requests through the client**

MCP sampling allows servers to request LLM completions through the client, enabling sophisticated agentic behaviors where the server can ask the model for guidance.
</details>

---

### Question 39

**What security principle should MCP servers follow?**

A) Trust all input without validation  
B) Validate all inputs and implement least privilege  
C) Grant full system access  
D) Disable authentication for simplicity  

<details>
<summary>✅ Answer</summary>

**B) Validate all inputs and implement least privilege**

MCP servers should validate all inputs, implement the principle of least privilege, and carefully control what actions tools can perform.
</details>

---

### Question 40

**How does Claude Code integrate with MCP?**

A) It doesn't support MCP  
B) Through the `claude mcp` commands and configuration files  
C) By compiling MCP servers into the CLI  
D) Through environment variables only  

<details>
<summary>✅ Answer</summary>

**B) Through the `claude mcp` commands and configuration files**

Claude Code integrates with MCP through `claude mcp` commands (like `claude mcp init`, `claude mcp add`) and configuration files that specify which MCP servers to use.
</details>

---

## Section E: Architecture & Best Practices (Questions 41-50)

### Question 41

**What is the recommended architecture for a production Claude application?**

A) Direct API calls from frontend  
B) Backend proxy with rate limiting, caching, and monitoring  
C) Direct database connections from Claude  
D) No architecture needed — just use the API  

<details>
<summary>✅ Answer</summary>

**B) Backend proxy with rate limiting, caching, and monitoring**

Production applications should use a backend proxy that handles rate limiting, caching, monitoring, and error handling before forwarding requests to the Claude API.
</details>

---

### Question 42

**Which technique helps reduce costs for repeated system prompts?**

A) Using shorter prompts  
B) Prompt caching  
C) Reducing temperature  
D) Using a different model  

<details>
<summary>✅ Answer</summary>

**B) Prompt caching**

Prompt caching stores processed versions of repeated content (like system prompts), reducing input token costs for subsequent requests by up to 90%.
</details>

---

### Question 43

**What is RAG (Retrieval-Augmented Generation)?**

A) A model training technique  
B) A pattern that retrieves relevant documents to augment Claude's context  
C) A way to generate training data  
D) A prompt engineering method  

<details>
<summary>✅ Answer</summary>

**B) A pattern that retrieves relevant documents to augment Claude's context**

RAG retrieves relevant documents from a knowledge base and includes them in Claude's context, enabling the model to answer questions based on specific, up-to-date information.
</details>

---

### Question 44

**Which observability practice is most important for Claude applications?**

A) Logging only errors  
B) Tracking token usage, latency, and error rates  
C) No monitoring needed  
D) Only tracking user satisfaction  

<details>
<summary>✅ Answer</summary>

**B) Tracking token usage, latency, and error rates**

Comprehensive observability includes tracking token usage (for cost), latency (for performance), error rates (for reliability), and user feedback (for quality).
</details>

---

### Question 45

**What is the purpose of a structured output approach?**

A) To make responses look nicer  
B) To ensure Claude's responses follow a predictable format (like JSON)  
C) To reduce token usage  
D) To increase creativity  

<details>
<summary>✅ Answer</summary>

**B) To ensure Claude's responses follow a predictable format (like JSON)**

Structured output approaches use system prompts, examples, and sometimes tool use to ensure Claude's responses follow a specific format, making them programmatically parseable.
</details>

---

### Question 46

**When should you use Claude Opus over Claude Sonnet?**

A) For all tasks — Opus is always better  
B) For complex reasoning, research, and tasks requiring the highest capability  
C) For simple classification tasks  
D) Never — Sonnet is always sufficient  

<details>
<summary>✅ Answer</summary>

**B) For complex reasoning, research, and tasks requiring the highest capability**

Claude Opus is the most capable model and should be used for complex tasks that require advanced reasoning, while Sonnet offers the best balance for most use cases.
</details>

---

### Question 47

**What is the best practice for API key management in production?**

A) Hardcode keys in source code  
B) Use environment variables or a secrets manager  
C) Share keys via email  
D) Commit keys to version control  

<details>
<summary>✅ Answer</summary>

**B) Use environment variables or a secrets manager**

API keys should be stored in environment variables or a dedicated secrets manager, never hardcoded in source code or committed to version control.
</details>

---

### Question 48

**What is the recommended approach for handling long documents that exceed the context window?**

A) Truncate the document randomly  
B) Use chunking with RAG to retrieve relevant sections  
C) Ask Claude to memorize the document  
D) Split into separate API calls without context  

<details>
<summary>✅ Answer</summary>

**B) Use chunking with RAG to retrieve relevant sections**

For documents exceeding the context window, use chunking (splitting into smaller pieces) combined with RAG to retrieve only the most relevant sections for each query.
</details>

---

### Question 49

**What is a key consideration when deploying MCP servers in production?**

A) Disable all security for performance  
B) Implement proper authentication, authorization, and network security  
C) Use only local communication  
D) Avoid monitoring  

<details>
<summary>✅ Answer</summary>

**B) Implement proper authentication, authorization, and network security**

Production MCP deployments need proper security: authentication, authorization, input validation, network security, and monitoring.
</details>

---

### Question 50

**Which strategy helps prevent hallucination in Claude responses?**

A) Using higher temperature  
B) Providing grounded context and asking Claude to cite sources  
C) Using shorter prompts  
D) Disabling the system prompt  

<details>
<summary>✅ Answer</summary>

**B) Providing grounded context and asking Claude to cite sources**

Grounding Claude with specific context and explicitly asking it to cite sources from the provided material helps reduce hallucination and improves factual accuracy.
</details>

---

## 📊 Scoring Guide

| Score Range | Assessment |
|-------------|------------|
| 45-50 (90-100%) | Excellent — You're well prepared! |
| 40-44 (80-89%) | Good — Review missed topics |
| 35-39 (70-79%) | Fair — More study needed |
| Below 35 (<70%) | Needs improvement — Review all chapters |

---

[⬅️ Back to Index](../README.md) | [➡️ Next: Practice Exam 2](./practice-exam-2.md)