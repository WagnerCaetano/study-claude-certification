# Practice Exam 3: Claude Certified Architect (Applied Knowledge)

> **Time Limit:** 90 minutes | **Questions:** 40 | **Passing Score:** 80%

---

[Back: Practice Exam 2](./practice-exam-2.md) | [Next: Practice Exam 4](./practice-exam-4.md)

---

## Questions

### Q1: Which Python code correctly creates an Anthropic client?

A) `client = anthropic.Client()`  
B) `client = anthropic.Anthropic()`  
C) `client = Anthropic()`  
D) `client = new Anthropic()`  

**Answer: B** - The correct way is `anthropic.Anthropic()`. It auto-reads the `ANTHROPIC_API_KEY` env var.

---

### Q2: What does `response.content[0].text` return in the Python SDK?

A) The full conversation history  
B) The first text block of Claude's response  
C) The API key used  
D) The model name  

**Answer: B** - `response.content` is a list of content blocks. `[0].text` gets the text of the first block.

---

### Q3: Which code snippet correctly implements streaming?

A) `response = client.messages.create(stream=True, ...)`  
B) `with client.messages.stream(...) as stream: for text in stream.text_stream: print(text)`  
C) `response = client.stream(...)`  
D) `for chunk in client.messages.create(...): print(chunk)`  

**Answer: B** - Use `client.messages.stream()` context manager with `text_stream` iterator.

---

### Q4: In tool use, what type is `response.content` when Claude wants to use a tool?

A) A single text string  
B) A list containing both text and tool_use blocks  
C) An error object  
D) A JSON string  

**Answer: B** - When using tools, `response.content` is a list that may contain both `text` and `tool_use` type blocks.

---

### Q5: What is the correct structure for a tool_result message?

A) `{"role": "tool", "content": result}`  
B) `{"role": "user", "content": [{"type": "tool_result", "tool_use_id": "...", "content": "..."}]}`  
C) `{"role": "assistant", "tool_result": result}`  
D) `{"type": "tool_result", "result": data}`  

**Answer: B** - Tool results are sent as `user` role messages with `tool_result` content blocks.

---

### Q6: Which MCP server method registers a tool in TypeScript?

A) `server.addTool(...)`  
B) `server.tool(...)`  
C) `server.registerTool(...)`  
D) `server.createTool(...)`  

**Answer: B** - The `McpServer.tool()` method registers a new tool.

---

### Q7: What does `z.object({...})` define in MCP tool registration?

A) The tool name  
B) The tool description  
C) The input schema using Zod validation  
D) The output format  

**Answer: C** - Zod schemas define the tool's input parameters with validation.

---

### Q8: Which command initializes MCP in Claude Code?

A) `claude init mcp`  
B) `claude mcp init`  
C) `mcp init`  
D) `claude setup mcp`  

**Answer: B** - `claude mcp init` initializes MCP configuration for the project.

---

### Q9: What is the `stop_reason` when Claude hits a stop sequence?

A) `end_turn`  
B) `max_tokens`  
C) `stop_sequence`  
D) `complete`  

**Answer: C** - `stop_sequence` indicates a defined stop sequence was encountered.

---

### Q10: Which model identifier format is correct?

A) `claude-4-sonnet`  
B) `claude-sonnet-4-20250514`  
C) `sonnet-4-claude`  
D) `claude.sonnet.4`  

**Answer: B** - The format is `claude-{family}-{version}-{date}`.

---

### Q11: What error type indicates you've exceeded your plan's request rate?

A) `BadRequestError`  
B) `AuthenticationError`  
C) `RateLimitError`  
D) `NotFoundError`  

**Answer: C** - `RateLimitError` is raised when exceeding allowed request rates.

---

### Q12: Which parameter controls randomness: 0=deterministic, 1=most random?

A) `max_tokens`  
B) `top_k`  
C) `temperature`  
D) `model`  

**Answer: C** - `temperature` controls randomness from 0.0 (deterministic) to 1.0 (creative).

---

### Q13: What is the purpose of `cache_control` in a content block?

A) To delete cached data  
B) To mark content as eligible for prompt caching  
C) To control browser caching  
D) To disable caching  

**Answer: B** - `cache_control: {"type": "ephemeral"}` marks content for prompt caching.

---

### Q14: In a ReAct agent loop, when should you stop?

A) After exactly 3 iterations  
B) When `stop_reason == "end_turn"` or max iterations reached  
C) Never  
D) When the user sends a message  

**Answer: B** - Stop when Claude finishes naturally (`end_turn`) or max iterations prevent infinite loops.

---

### Q15: What is the primary benefit of multi-agent orchestration?

A) It's cheaper  
B) Each agent can specialize in a specific task  
C) It requires less code  
D) It uses fewer API calls  

**Answer: B** - Specialized agents handle specific tasks better than one general-purpose agent.

---

### Q16: Which HTTP status code indicates an authentication error?

A) 400  
B) 401  
C) 429  
D) 500  

**Answer: B** - 401 Unauthorized indicates invalid or missing API key.

---

### Q17: What does RAG stand for?

A) Random Access Generation  
B) Retrieval-Augmented Generation  
C) Real-time AI Gateway  
D) Recursive Agent Graph  

**Answer: B** - RAG = Retrieval-Augmented Generation, a pattern for grounding responses in data.

---

### Q18: What is the Anthropic console used for?

A) Writing code  
B) Managing API keys, monitoring usage, and configuring billing  
C) Training models  
D) Building MCP servers  

**Answer: B** - The console (console.anthropic.com) manages keys, usage, and billing.

---

### Q19: Which SDK method sends a message to Claude in Node.js?

A) `client.send()`  
B) `client.messages.create()`  
C) `client.chat()`  
D) `client.generate()`  

**Answer: B** - `client.messages.create()` is the method for sending messages in all SDKs.

---

### Q20: What is the recommended way to store API keys?

A) In source code  
B) In environment variables  
C) In README files  
D) In commit messages  

**Answer: B** - Always use environment variables or secrets managers for API keys.

---

### Q21: What does Claude Code's `CLAUDE.md` file support?

A) Only plain text  
B) Markdown with project instructions, conventions, and context  
C) Only JSON configuration  
D) Only Python code  

**Answer: B** - CLAUDE.md supports Markdown for project instructions and conventions.

---

### Q22: Which MCP primitive would you use to expose a database query capability?

A) Prompt  
B) Resource (for read-only queries)  
C) Neither  
D) Both  

**Answer: B** - Resources expose read-only data like database queries without side effects.

---

### Q23: What happens when you set `max_tokens=1`?

A) Claude writes one sentence  
B) Claude generates exactly 1 token then stops  
C) Claude ignores the parameter  
D) An error occurs  

**Answer: B** - `max_tokens=1` limits output to a single token, then generation stops.

---

### Q24: Which is NOT a valid `stop_reason` value?

A) `end_turn`  
B) `max_tokens`  
C) `stop_sequence`  
D) `timeout`  

**Answer: D** - `timeout` is not a valid stop_reason. Valid values are `end_turn`, `max_tokens`, and `stop_sequence`.

---

### Q25: What is the benefit of using system prompts vs user messages?

A) System prompts are cheaper  
B) System prompts set persistent behavior without being part of the conversation flow  
C) System prompts are faster  
D) There is no difference  

**Answer: B** - System prompts define behavior for the entire conversation separately from the message history.

---

### Q26: Which approach best prevents prompt injection?

A) Use longer prompts  
B) Separate user input from instructions using XML tags, and validate inputs  
C) Never use system prompts  
D) Use temperature 0  

**Answer: B** - XML tags separate instructions from user input, and input validation prevents injection.

---

### Q27: What is the context window for Claude Haiku 3.5?

A) 8K tokens  
B) 32K tokens  
C) 128K tokens  
D) 200K tokens  

**Answer: D** - Claude Haiku 3.5 supports a 200K token context window.

---

### Q28: In MCP, what is a "transport"?

A) A way to move files between computers  
B) The communication mechanism between MCP client and server  
C) A data transformation layer  
D) A load balancing strategy  

**Answer: B** - Transport is the communication mechanism (stdio for local, HTTP+SSE for remote).

---

### Q29: What should you do when Claude returns an empty response?

A) Retry with a clearer prompt and check max_tokens  
B) Delete your API key  
C) Switch models  
D) Give up  

**Answer: A** - Check if max_tokens is too low, or rephrase the prompt for clarity.

---

### Q30: Which is the most important metric to track in production?

A) Number of users  
B) Error rate, latency, and cost (token usage)  
C) Uptime only  
D) Server CPU usage  

**Answer: B** - Error rate, latency, and token usage are the core metrics for Claude applications.

---

### Q31: What is the purpose of guardrails in an agent system?

A) To make the agent faster  
B) To constrain agent behavior and prevent unsafe actions  
C) To improve response quality  
D) To reduce costs  

**Answer: B** - Guardrails prevent agents from taking unsafe or unintended actions.

---

### Q32: Which code pattern correctly handles a tool use response?

A) Check `response.stop_reason == "tool_use"`, extract tool calls, execute, return results  
B) Ignore tool calls and continue  
C) Always execute all tools regardless of Claude's response  
D) Use a separate API for tool execution  

**Answer: A** - Check stop_reason, extract tool_use blocks, execute the tool, return tool_result.

---

### Q33: What is MCP sampling used for?

A) Data collection  
B) Allowing MCP servers to request LLM completions through the client  
C) Token counting  
D) Rate limiting  

**Answer: B** - Sampling lets servers request LLM completions, enabling agentic behavior.

---

### Q34: Which practice helps with prompt debugging?

A) Use only one massive prompt  
B) Break prompts into labeled sections with XML tags and test incrementally  
C) Never test prompts  
D) Use only system prompts  

**Answer: B** - XML tags and incremental testing help identify which prompt sections need adjustment.

---

### Q35: What is the best model choice for real-time chat applications?

A) Claude Opus 4  
B) Claude Sonnet 4  
C) Claude Haiku 3.5  
D) Any model works equally  

**Answer: C** - Haiku's low latency makes it best for real-time chat where speed matters most.

---

### Q36: What is the recommended retry strategy for API errors?

A) Retry immediately, 10 times  
B) Exponential backoff: 1s, 2s, 4s, 8s  
C) Wait 60 seconds between each retry  
D) Never retry  

**Answer: B** - Exponential backoff progressively increases wait time between retries.

---

### Q37: How do you add a new MCP server to Claude Desktop?

A) Reinstall Claude Desktop  
B) Add the server config to `claude_desktop_config.json`  
C) Use the `claude mcp` CLI command  
D) Email Anthropic support  

**Answer: B** - Add server configuration to the `claude_desktop_config.json` file.

---

### Q38: What does "few-shot prompting" mean?

A) Using very few tokens  
B) Providing examples in the prompt to guide Claude's output format  
C) Taking only a few shots at getting the right answer  
D) Using a small model  

**Answer: B** - Few-shot prompting includes examples in the prompt to demonstrate the expected output.

---

### Q39: Which is NOT a valid content type in Claude's response?

A) `text`  
B) `tool_use`  
C) `image`  
D) `thinking`  

**Answer: C** - `image` is not a valid response content type. Claude responds with `text` and `tool_use` blocks.

---

### Q40: What is the primary benefit of prompt caching?

A) Faster responses  
B) Reduced input token costs for repeated content  
C) Better output quality  
D) More creative responses  

**Answer: B** - Prompt caching reduces costs by caching processed input tokens across requests.

---

## Scoring

| Score | Assessment |
|-------|------------|
| 36-40 (90-100%) | Excellent |
| 32-35 (80-89%) | Good |
| 28-31 (70-79%) | Fair |
| <28 (<70%) | Review needed |

---

[Back: Practice Exam 2](./practice-exam-2.md) | [Next: Practice Exam 4](./practice-exam-4.md)
