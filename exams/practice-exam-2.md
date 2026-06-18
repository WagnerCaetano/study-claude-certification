# 📝 Practice Exam 2: Claude Certified Architect (Scenario-Based)

> **Time Limit:** 90 minutes | **Questions:** 50 | **Passing Score:** 80%

---

[⬅️ Back: Practice Exam 1](./practice-exam-1.md) | [➡️ Next: Practice Exam 3](./practice-exam-3.md)

---

## Instructions

- This exam features **scenario-based** and **varied-format** questions
- Formats include: multiple choice, true/false, scenario analysis, and "select all that apply"
- Choose the **best** answer(s) for each question

---

## Section A: True or False (Questions 1-10)

### Question 1

**True or False: Claude maintains conversation state between separate API calls.**

A) True  
B) False  

<details>
<summary>✅ Answer</summary>

**B) False**

The Claude API is stateless. Each request must include the full conversation history.
</details>

---

### Question 2

**True or False: MCP (Model Context Protocol) is a proprietary Anthropic protocol that only works with Claude.**

A) True  
B) False  

<details>
<summary>✅ Answer</summary>

**B) False**

MCP is an open protocol that any AI model can implement. It's not limited to Claude.
</details>

---

### Question 3

**True or False: The `system` parameter is part of the `messages` array in the Claude API.**

A) True  
B) False  

<details>
<summary>✅ Answer</summary>

**B) False**

The `system` parameter is a separate top-level parameter, not part of the `messages` array.
</details>

---

### Question 4

**True or False: Setting `temperature=0` guarantees the exact same output every time.**

A) True  
B) False  

<details>
<summary>✅ Answer</summary>

**B) False**

While `temperature=0` produces highly deterministic output, there may still be slight variations due to floating-point operations. It's not guaranteed to be identical across calls.
</details>

---

### Question 5

**True or False: Claude Code requires Node.js to be installed.**

A) True  
B) False  

<details>
<summary>✅ Answer</summary>

**A) True**

Claude Code is distributed as an npm package and requires Node.js 18+ to run.
</details>

---

### Question 6

**True or False: MCP Resources can have side effects (modify data).**

A) True  
B) False  

<details>
<summary>✅ Answer</summary>

**B) False**

MCP Resources are read-only data sources. MCP Tools are used for actions that may have side effects.
</details>

---

### Question 7

**True or False: `max_tokens` controls the input length, not the output length.**

A) True  
B) False  

<details>
<summary>✅ Answer</summary>

**B) False**

`max_tokens` controls the **maximum output length**. Input length is governed by the context window.
</details>

---

### Question 8

**True or False: Prompt caching can reduce input token costs by up to 90%.**

A) True  
B) False  

<details>
<summary>✅ Answer</summary>

**A) True**

Prompt caching can reduce input token costs by up to 90% for repeated content across requests.
</details>

---

### Question 9

**True or False: Claude Haiku is the best model for complex multi-step reasoning tasks.**

A) True  
B) False  

<details>
<summary>✅ Answer</summary>

**B) False**

Claude Haiku is optimized for speed and cost, not complex reasoning. Claude Opus is best for complex multi-step reasoning.
</details>

---

### Question 10

**True or False: In the tool use flow, the `tool_use_id` must match between the tool call and the tool result.**

A) True  
B) False  

<details>
<summary>✅ Answer</summary>

**A) True**

The `tool_use_id` in the tool result must match the `id` from the corresponding tool call block to correctly link the result.
</details>

---

## Section B: Scenario-Based Questions (Questions 11-25)

### Question 11

**Scenario: You're building a customer support chatbot. Users report that Claude sometimes mentions products your company doesn't sell. What technique should you implement?**

A) Increase temperature  
B) Implement RAG with your actual product catalog  
C) Use Claude Haiku instead  
D) Remove the system prompt  

<details>
<summary>✅ Answer</summary>

**B) Implement RAG with your actual product catalog**

This is a hallucination problem. RAG (Retrieval-Augmented Generation) with your actual product catalog will ground Claude's responses in factual data, preventing it from inventing products.
</details>

---

### Question 12

**Scenario: Your application processes 50,000 requests/day. Costs are $500/day using Claude Sonnet. How can you most effectively reduce costs?**

A) Switch all requests to Claude Opus  
B) Analyze request complexity and route simple ones to Haiku, complex ones to Sonnet  
C) Reduce max_tokens to 10 for all requests  
D) Stop using the API  

<details>
<summary>✅ Answer</summary>

**B) Analyze request complexity and route simple ones to Haiku, complex ones to Sonnet**

A tiered routing approach sends simple tasks (classification, extraction) to cheaper Haiku and complex tasks to Sonnet, optimizing cost without sacrificing quality.
</details>

---

### Question 13

**Scenario: Your agent enters an infinite loop, repeatedly calling the same tool. What should you add?**

A) More tools to distract the agent  
B) A maximum iteration limit and loop detection  
C) A higher temperature  
D) A longer system prompt  

<details>
<summary>✅ Answer</summary>

**B) A maximum iteration limit and loop detection**

Maximum iteration limits prevent infinite loops. Loop detection (checking if the same tool is called with the same parameters repeatedly) can break the cycle.
</details>

---

### Question 14

**Scenario: You need to build an MCP server that connects Claude to your company's internal API. Which primitive should you use?**

A) Resource  
B) Prompt  
C) Tool  
D) All of the above  

<details>
<summary>✅ Answer</summary>

**C) Tool**

An MCP Tool is appropriate for making API calls that may have side effects (creating records, updating data). If you only needed to read data, a Resource would suffice.
</details>

---

### Question 15

**Scenario: A user uploads a 300-page PDF for analysis. What approach should you use?**

A) Send the entire PDF in one request  
B) Chunk the PDF, use embeddings to find relevant sections, and include only those  
C) Tell the user PDFs aren't supported  
D) Ask Claude to memorize the PDF  

<details>
<summary>✅ Answer</summary>

**B) Chunk the PDF, use embeddings to find relevant sections, and include only those**

A 300-page PDF likely exceeds the context window. Chunking with RAG retrieval is the standard approach for large documents.
</details>

---

### Question 16

**Scenario: Your team wants Claude to always respond in JSON format. What's the most reliable approach?**

A) Ask nicely in the user message  
B) Use a system prompt specifying JSON output, provide examples, and set `stop_sequences`  
C) Use temperature 0  
D) There's no reliable way  

<details>
<summary>✅ Answer</summary>

**B) Use a system prompt specifying JSON output, provide examples, and set `stop_sequences`**

A combination of a clear system prompt, few-shot examples, and optionally using tool use for structured output provides the most reliable JSON responses.
</details>

---

### Question 17

**Scenario: You're configuring Claude Code for a team project. Where should you put project-specific instructions?**

A) In environment variables  
B) In a `CLAUDE.md` file in the project root  
C) In the system registry  
D) In a database  

<details>
<summary>✅ Answer</summary>

**B) In a `CLAUDE.md` file in the project root**

`CLAUDE.md` is the standard file for providing Claude Code with project-specific context, conventions, and instructions.
</details>

---

### Question 18

**Scenario: Your API call returns `stop_reason: "max_tokens"`. What should you do?**

A) Increase `max_tokens` and regenerate  
B) Ignore it — the response is fine  
C) Use a different model  
D) Delete the API key  

<details>
<summary>✅ Answer</summary>

**A) Increase `max_tokens` and regenerate**

`stop_reason: "max_tokens"` means the response was truncated. You should increase the `max_tokens` value and retry the request to get the complete response.
</details>

---

### Question 19

**Scenario: You need to deploy an MCP server that accesses sensitive company data. What security measures should you implement? (Select all that apply)**

A) Input validation  
B) Authentication  
C) Network encryption (TLS)  
D) Access control / authorization  
E) Audit logging  

<details>
<summary>✅ Answer</summary>

**All of the above (A, B, C, D, E)**

All security measures are essential when handling sensitive data: validate inputs, require authentication, encrypt communications, implement access control, and log all access.
</details>

---

### Question 20

**Scenario: Your streaming response suddenly stops mid-sentence. What likely happened?**

A) The API crashed  
B) You hit the `max_tokens` limit  
C) The internet is down  
D) Claude forgot what it was saying  

<details>
<summary>✅ Answer</summary>

**B) You hit the `max_tokens` limit**

When streaming stops mid-response, the most common cause is hitting the `max_tokens` limit. Increase it to allow longer responses.
</details>

---

### Question 21

**Scenario: You want to use Claude for code review in CI/CD. Which model and approach is most cost-effective?**

A) Claude Opus with full repository context  
B) Claude Sonnet with only the changed files and diff  
C) Claude Haiku with no context  
D) Claude Opus with minimal context  

<details>
<summary>✅ Answer</summary>

**B) Claude Sonnet with only the changed files and diff**

Claude Sonnet provides excellent code understanding at a reasonable cost. Sending only changed files and diffs keeps token usage manageable.
</details>

---

### Question 22

**Scenario: Your agent needs to access a PostgreSQL database, a REST API, and a file system. What architecture should you use?**

A) One massive tool that handles everything  
B) Separate MCP servers for each data source  
C) Direct database connections from Claude  
D) Write everything to files first  

<details>
<summary>✅ Answer</summary>

**B) Separate MCP servers for each data source**

Separate MCP servers for each data source follow the single responsibility principle and are easier to maintain, secure, and scale.
</details>

---

### Question 23

**Scenario: Users report your Claude app is slow. Analytics show average response time is 8 seconds. What's the first thing to check?**

A) Switch to a faster model immediately  
B) Analyze input/output token counts and identify optimization opportunities  
C) Add more API keys  
D) Disable streaming  

<details>
<summary>✅ Answer</summary>

**B) Analyze input/output token counts and identify optimization opportunities**

First understand the bottleneck — are inputs too large? Are outputs unnecessarily long? Can prompt caching help? Data-driven optimization beats premature changes.
</details>

---

### Question 24

**Scenario: You need Claude to extract structured data from invoices. What's the most reliable approach?**

A) Ask Claude to describe the invoice in free text  
B) Use a system prompt with the desired JSON schema and few-shot examples  
C) Use Claude Haiku without a system prompt  
D) Train a custom model  

<details>
<summary>✅ Answer</summary>

**B) Use a system prompt with the desired JSON schema and few-shot examples**

A structured system prompt with a clear schema and examples provides the most reliable data extraction results.
</details>

---

### Question 25

**Scenario: Your MCP server needs to handle both read (query data) and write (update data) operations. How should you design it?**

A) Use Tools for both reads and writes  
B) Use Resources for reads, Tools for writes  
C) Use Resources for both reads and writes  
D) Use Prompts for reads, Tools for writes  

<details>
<summary>✅ Answer</summary>

**B) Use Resources for reads, Tools for writes**

Following MCP conventions: Resources for read-only data access, Tools for operations with side effects (writes, updates, deletes).
</details>

---

## Section C: Fill in the Blank & Short Answer (Questions 26-35)

### Question 26

**The Claude API uses the `______` HTTP header for authentication.**

A) Authorization  
B) x-api-key  
C) api-key  
D) authentication  

<details>
<summary>✅ Answer</summary>

**B) x-api-key**

The Anthropic API uses the `x-api-key` header to pass the API key for authentication.
</details>

---

### Question 27

**MCP uses `______` transport for local communication and HTTP with SSE for remote communication.**

A) WebSocket  
B) gRPC  
C) stdio  
D) TCP  

<details>
<summary>✅ Answer</summary>

**C) stdio**

MCP uses standard I/O (stdio) for local communication between client and server processes.
</details>

---

### Question 28

**The three principles behind Claude's training are: Helpful, Harmless, and ______.**

A) Fast  
B) Cheap  
C) Honest  
D) Creative  

<details>
<summary>✅ Answer</summary>

**C) Honest**

Claude is designed to be Helpful, Harmless, and Honest — the "HHH" framework that guides Anthropic's approach to AI safety.
</details>

---

### Question 29

**When Claude's response is truncated due to token limits, the `stop_reason` will be `______`.**

A) end_turn  
B) stop_sequence  
C) max_tokens  
D) error  

<details>
<summary>✅ Answer</summary>

**C) max_tokens**

The `stop_reason` of `max_tokens` indicates the response was truncated because it hit the maximum token limit.
</details>

---

### Question 30

**The agent pattern that alternates between reasoning and acting is called `______`.**

A) Map-Reduce  
B) ReAct  
C) Plan-and-Execute  
D) Chain-of-Thought  

<details>
<summary>✅ Answer</summary>

**B) ReAct**

The ReAct (Reason + Act) pattern alternates between thinking about what to do and executing actions.
</details>

---

### Question 31

**In the Claude API, the `______` parameter sets Claude's behavior for the entire conversation.**

A) messages  
B) system  
C) temperature  
D) model  

<details>
<summary>✅ Answer</summary>

**B) system**

The `system` parameter defines Claude's role and behavior instructions for the entire conversation, separate from the messages array.
</details>

---

### Question 32

**The file `______` provides Claude Code with project-specific instructions and conventions.**

A) README.md  
B) package.json  
C) CLAUDE.md  
D) .env  

<details>
<summary>✅ Answer</summary>

**C) CLAUDE.md**

`CLAUDE.md` is the standard configuration file for providing Claude Code with project context and instructions.
</details>

---

### Question 33

**When using prompt caching, you mark content blocks with `cache_control` type `______`.**

A) permanent  
B) persistent  
C) ephemeral  
D) temporary  

<details>
<summary>✅ Answer</summary>

**C) ephemeral**

The `cache_control` type is `"ephemeral"`, indicating the cache can be evicted when not needed.
</details>

---

### Question 34

**The pattern of retrieving relevant documents to enhance Claude's context is called `______`.**

A) RAG  
B) MCP  
C) CAI  
D) ReAct  

<details>
<summary>✅ Answer</summary>

**A) RAG**

RAG (Retrieval-Augmented Generation) retrieves relevant documents from a knowledge base to augment the model's context.
</details>

---

### Question 35

**The Claude model naming pattern follows: `claude-{family}-{version}-______`.**

A) capability  
B) date  
C) size  
D) tier  

<details>
<summary>✅ Answer</summary>

**B) date**

The naming pattern ends with a date, e.g., `claude-sonnet-4-20250514`.
</details>

---

## Section D: Advanced Scenarios (Questions 36-50)

### Question 36

**Scenario: Your company needs to process legal documents. Accuracy is critical — cost is secondary. Which model and configuration should you use?**

A) Claude Haiku, temperature 0.7  
B) Claude Opus, temperature 0.0, with RAG  
C) Claude Sonnet, temperature 1.0  
D) Claude Haiku, temperature 0.0  

<details>
<summary>✅ Answer</summary>

**B) Claude Opus, temperature 0.0, with RAG**

For critical accuracy tasks: use the most capable model (Opus), deterministic output (temperature 0.0), and RAG for grounded responses from actual legal documents.
</details>

---

### Question 37

**Scenario: You're building a multi-tenant SaaS app. Each tenant has different system prompts and tools. What architecture pattern should you use?**

A) One hardcoded configuration for all tenants  
B) Dynamic configuration loading based on tenant context  
C) Separate API keys per tenant  
D) No system prompts  

<details>
<summary>✅ Answer</summary>

**B) Dynamic configuration loading based on tenant context**

A multi-tenant architecture should dynamically load tenant-specific configurations (system prompts, tools, MCP servers) based on the authenticated tenant context.
</details>

---

### Question 38

**Scenario: Your agent needs to research a topic, write a report, then review its own work. Which pattern fits best?**

A) Single-step tool call  
B) Multi-agent with Planner, Writer, and Reviewer agents  
C) Direct API call without tools  
D) Simple prompt chain  

<details>
<summary>✅ Answer</summary>

**B) Multi-agent with Planner, Writer, and Reviewer agents**

Complex multi-phase tasks benefit from specialized agents: a Planner for research strategy, a Writer for drafting, and a Reviewer for quality control.
</details>

---

### Question 39

**Scenario: Your MCP server will be deployed to production. Which transport should you use for remote access?**

A) stdio  
B) HTTP with SSE  
C) Direct memory access  
D) File system  

<details>
<summary>✅ Answer</summary>

**B) HTTP with SSE**

For remote/production deployments, MCP uses HTTP with Server-Sent Events (SSE) for communication. stdio is only for local communication.
</details>

---

### Question 40

**Scenario: You want to implement cost tracking for your Claude application. What should you monitor?**

A) Only the number of API calls  
B) Input tokens, output tokens, model used, and latency per request  
C) Only errors  
D) User satisfaction only  

<details>
<summary>✅ Answer</summary>

**B) Input tokens, output tokens, model used, and latency per request**

Comprehensive cost tracking requires monitoring input tokens, output tokens, which model was used (different pricing), and latency for performance analysis.
</details>

---

### Question 41

**Scenario: You need to implement A/B testing between two different system prompts. What's the best approach?**

A) Change prompts manually each week  
B) Implement a configuration-driven prompt management system with randomized assignment  
C) Use the same prompt for all users  
D) Ask Claude to choose the best prompt  

<details>
<summary>✅ Answer</summary>

**B) Implement a configuration-driven prompt management system with randomized assignment**

A proper A/B testing framework uses randomized user assignment, configuration-driven prompts, and metrics tracking to determine statistical significance.
</details>

---

### Question 42

**Scenario: Your application receives a `RateLimitError`. Your current retry logic waits 1 second between retries. What should you improve?**

A) Give up immediately  
B) Implement exponential backoff starting with 1 second  
C) Remove retry logic entirely  
D) Wait 60 seconds between every retry  

<details>
<summary>✅ Answer</summary>

**B) Implement exponential backoff starting with 1 second**

Exponential backoff (1s, 2s, 4s, 8s...) is more effective than fixed delays. It starts quick but gives the API time to recover if needed.
</details>

---

### Question 43

**Scenario: You need Claude to process images from user uploads. Which model capability do you leverage?**

A) Text-to-speech  
B) Vision (multimodal)  
C) Code generation  
D) Tool use  

<details>
<summary>✅ Answer</summary>

**B) Vision (multimodal)**

Claude's vision capability allows it to analyze images passed as base64-encoded data in the messages.
</details>

---

### Question 44

**Scenario: Your team is debating between using Claude API directly vs. building MCP servers. When is MCP the better choice?**

A) Always — MCP is always better  
B) When you need standardized, reusable integrations with multiple tools and data sources  
C) Never — the API is always better  
D) Only for Python projects  

<details>
<summary>✅ Answer</summary>

**B) When you need standardized, reusable integrations with multiple tools and data sources**

MCP shines when you need standardized integrations that can be reused across different AI applications and clients. For simple API calls, direct API usage is fine.
</details>

---

### Question 45

**Scenario: Your production Claude app has intermittent 500 errors. What should you do?**

A) Ignore them — they'll go away  
B) Implement retry logic for 5xx errors with exponential backoff and alerting  
C) Switch to a different AI provider  
D) Delete and recreate the API key  

<details>
<summary>✅ Answer</summary>

**B) Implement retry logic for 5xx errors with exponential backoff and alerting**

Server errors (5xx) are often transient. Implement retries with exponential backoff, and set up alerting if error rates exceed a threshold.
</details>

---

### Question 46

**Scenario: You're designing a system where Claude needs to maintain context across multiple user sessions spanning days. What approach should you use?**

A) Keep the API connection open for days  
B) Store conversation summaries in a database and reload as context  
C) It's impossible — Claude can't do this  
D) Use a single massive prompt  

<details>
<summary>✅ Answer</summary>

**B) Store conversation summaries in a database and reload as context**

For long-term context: store conversation summaries and key facts in a database, then reload relevant context at the start of each new session.
</details>

---

### Question 47

**Scenario: Your compliance team requires all Claude interactions to be logged. What should you log?**

A) Only errors  
B) All inputs, outputs, timestamps, model used, and token counts  
C) Only user messages  
D) Only the first message  

<details>
<summary>✅ Answer</summary>

**B) All inputs, outputs, timestamps, model used, and token counts**

For compliance, log all inputs (prompts), outputs (responses), timestamps, model identifiers, and token counts. Ensure logging follows data privacy regulations.
</details>

---

### Question 48

**Scenario: Your MCP server needs to support both Claude Desktop and Claude Code. What protocol consideration is important?**

A) You need two separate servers  
B) Both clients use the same MCP protocol, so one server works for both  
C) Claude Desktop doesn't support MCP  
D) Claude Code requires a special transport  

<details>
<summary>✅ Answer</summary>

**B) Both clients use the same MCP protocol, so one server works for both**

MCP is a standard protocol. Both Claude Desktop and Claude Code implement the same MCP client specification, so one server can serve both.
</details>

---

### Question 49

**Scenario: You're building a content moderation system. What's the best approach?**

A) Use Claude without any safeguards  
B) Combine Claude's built-in safety with custom classification rules and human review  
C) Only use keyword filtering  
D) Let users moderate themselves  

<details>
<summary>✅ Answer</summary>

**B) Combine Claude's built-in safety with custom classification rules and human review**

A layered approach is best: Claude's built-in safety features + custom prompt-based classification + human review for edge cases.
</details>

---

### Question 50

**Scenario: Your application needs to handle 100 concurrent Claude API requests. What should you implement?**

A) Send all requests simultaneously  
B) Implement connection pooling, rate limiting, and queue management  
C) Use 100 different API keys  
D) Process requests one at a time  

<details>
<summary>✅ Answer</summary>

**B) Implement connection pooling, rate limiting, and queue management**

For high concurrency: use connection pooling, respect rate limits, implement request queuing, and handle backpressure gracefully.
</details>

---

## 📊 Scoring Guide

| Score Range | Assessment |
|-------------|------------|
| 45-50 (90-100%) | Excellent — Ready for the exam! |
| 40-44 (80-89%) | Good — Review weak areas |
| 35-39 (70-79%) | Fair — More study needed |
| Below 35 (<70%) | Needs improvement — Review all chapters |

---

[⬅️ Back: Practice Exam 1](./practice-exam-1.md) | [➡️ Next: Practice Exam 3](./practice-exam-3.md)