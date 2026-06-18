# Practice Exam 4: Claude Certified Architect (Final Assessment)

> **Time Limit:** 90 minutes | **Questions:** 40 | **Passing Score:** 80%

---

[Back: Practice Exam 3](./practice-exam-3.md) | [Back to Index](../README.md)

---

## Questions

### Q1: A company needs to classify 100K customer emails by sentiment. Which approach is most cost-effective?

A) Claude Opus with detailed analysis  
B) Claude Haiku with batch processing and a clear system prompt  
C) Manual classification  
D) Claude Sonnet with streaming  

**Answer: B** - Haiku is cheapest and fastest for high-volume classification tasks.

---

### Q2: Your agent calls the same tool 5 times with identical inputs. What should you implement?

A) More tools  
B) Caching and loop detection with a max iteration limit  
C) A larger context window  
D) Higher temperature  

**Answer: B** - Cache tool results to avoid redundant calls, and detect loops to break cycles.

---

### Q3: What is the correct order of the tool use flow?

A) Call API -> Get response -> Return to user  
B) Call API -> Check stop_reason -> Execute tool -> Return result -> Call API again  
C) Execute tool -> Call API -> Get response  
D) Define tools -> Call API -> Return response  

**Answer: B** - The flow: send request, check if tool_use, execute the tool, return result, call API again.

---

### Q4: Which configuration enables MCP in Claude Code at the project level?

A) `.mcp/config.json`  
B) `.claude/mcp.json` or running `claude mcp add`  
C) `package.json`  
D) `mcp.config.js`  

**Answer: B** - Use `.claude/mcp.json` or the `claude mcp add` command for project-level MCP config.

---

### Q5: You're getting 403 errors. What is likely the cause?

A) Rate limiting  
B) Invalid API key or insufficient permissions  
C) Network timeout  
D) Wrong model name  

**Answer: B** - 403 Forbidden indicates authentication/authorization issues with your API key.

---

### Q6: Which pattern is best for summarizing a 500-page book?

A) Send the entire book in one request  
B) Chunk by chapter, summarize each, then create a meta-summary  
C) Use a shorter prompt  
D) Ask Claude to skim  

**Answer: B** - Map-reduce: summarize each chunk, then combine for a meta-summary.

---

### Q7: What is the main advantage of using XML tags in prompts?

A) They reduce token count  
B) They clearly separate different sections of the prompt for Claude  
C) They make prompts executable  
D) They enable caching automatically  

**Answer: B** - XML tags create clear structure, helping Claude distinguish between instructions, context, and examples.

---

### Q8: Your production costs doubled overnight. What should you check first?

A) Change the API key  
B) Review token usage by model, endpoint, and user to identify the spike  
C) Switch to a free tier  
D) Delete the application  

**Answer: B** - Analyze usage patterns to identify what caused the cost increase before taking action.

---

### Q9: Which is NOT a benefit of streaming responses?

A) Lower perceived latency  
B) Reduced total token usage  
C) Real-time display of responses  
D) Better user experience for long responses  

**Answer: B** - Streaming doesn't reduce total token usage. It improves perceived latency by showing tokens as they're generated.

---

### Q10: What does the `anthropic-version` header ensure?

A) Latest features are used  
B) Backward compatibility with a specific API version  
C) Faster responses  
D) Lower costs  

**Answer: B** - It pins the API version, ensuring consistent behavior as the API evolves.

---

### Q11: For a content generation app producing marketing copy, which temperature is best?

A) 0.0  
B) 0.3  
C) 0.7  
D) 1.0  

**Answer: C** - Temperature 0.7 balances creativity with coherence for marketing copy.

---

### Q12: How should you handle PII (Personally Identifiable Information) in Claude requests?

A) Send all PII without restrictions  
B) Redact or anonymize PII before sending, and implement data handling policies  
C) Encrypt the entire request  
D) Don't use Claude for any PII-related tasks  

**Answer: B** - Redact/anonymize PII before sending to the API and implement proper data governance.

---

### Q13: What is the purpose of the `top_p` parameter?

A) Control maximum output tokens  
B) Limit token selection to those within cumulative probability p  
C) Set the model version  
D) Enable streaming  

**Answer: B** - `top_p` (nucleus sampling) limits next-token selection to the most probable tokens.

---

### Q14: Which architecture handles multiple data sources best?

A) One monolithic function  
B) Separate MCP servers with a unified client interface  
C) Direct API calls for each source  
D) Hardcoded data access  

**Answer: B** - Separate MCP servers per data source with a unified client interface for maintainability.

---

### Q15: What is chain-of-thought prompting?

A) Asking Claude to think step by step  
B) A specific API parameter  
C) A model training technique  
D) A way to chain multiple API calls  

**Answer: A** - Chain-of-thought prompts Claude to reason through problems step by step for better results.

---

### Q16: Which error indicates you sent invalid JSON?

A) 401  
B) 400 BadRequestError  
C) 429  
D) 500  

**Answer: B** - 400/BadRequestError indicates malformed request data including invalid JSON.

---

### Q17: What is the best way to test MCP servers locally?

A) Deploy to production and test there  
B) Use the MCP Inspector tool or test with Claude Desktop  
C) Email Anthropic support  
D) Read the source code  

**Answer: B** - MCP Inspector provides a local testing UI, or configure the server in Claude Desktop for testing.

---

### Q18: When should you use the Plan-and-Execute pattern over ReAct?

A) Always  
B) When tasks are predictable and benefit from upfront planning  
C) Never  
D) Only for simple tasks  

**Answer: B** - Plan-and-Execute is better when tasks have clear steps that benefit from planning before acting.

---

### Q19: What is the cost difference between cached and uncached input tokens?

A) They cost the same  
B) Cached tokens are approximately 90% cheaper  
C) Cached tokens are more expensive  
D) Caching is free  

**Answer: B** - Cached input tokens are approximately 90% cheaper than regular input tokens.

---

### Q20: Which is the most secure way to handle API keys in a Docker container?

A) Hardcode in the Dockerfile  
B) Pass via environment variables at runtime  
C) Store in a config file in the image  
D) Use the default key  

**Answer: B** - Pass API keys via environment variables at container runtime, never bake them into images.

---

### Q21: What does `response.usage` contain?

A) The response text  
B) Input and output token counts  
C) The API key used  
D) Error messages  

**Answer: B** - `response.usage` contains `input_tokens` and `output_tokens` counts.

---

### Q22: Which is a benefit of using Claude Code over the web interface?

A) It's free  
B) Direct integration with your codebase, MCP, and agentic workflows  
C) It doesn't require an API key  
D) It has more models  

**Answer: B** - Claude Code integrates with your local codebase, MCP servers, and supports agentic workflows.

---

### Q23: For an e-commerce product recommendation system, which architecture works best?

A) Direct API calls with hardcoded products  
B) RAG with product database + Claude for personalized recommendations  
C) No AI - just show random products  
D) Use Claude Opus for every query  

**Answer: B** - RAG retrieves relevant products from the database, Claude generates personalized recommendations.

---

### Q24: What is the minimum information needed in a tool definition?

A) Name only  
B) Name, description, and input_schema  
C) Description only  
D) Code implementation  

**Answer: B** - Tools require a `name`, `description`, and `input_schema` at minimum.

---

### Q25: How does Claude handle code in conversations?

A) It ignores code  
B) It can understand, write, debug, and explain code  
C) It only understands Python  
D) It executes code  

**Answer: B** - Claude can understand, generate, debug, and explain code across many languages.

---

### Q26: What is the purpose of the `model` parameter?

A) To select which Claude model processes the request  
B) To set the response format  
C) To configure caching  
D) To enable streaming  

**Answer: A** - The `model` parameter specifies which Claude model to use (e.g., `claude-sonnet-4-20250514`).

---

### Q27: Your MCP server returns an error. How do you debug it?

A) Ignore the error  
B) Check server logs, use MCP Inspector, verify transport config  
C) Reinstall everything  
D) Switch to direct API calls  

**Answer: B** - Check server logs, test with MCP Inspector, verify transport configuration and inputs.

---

### Q28: Which statement about Claude's vision capability is true?

A) It can only process PNG images  
B) It accepts base64-encoded images and can analyze their content  
C) It can generate images  
D) It requires a special model  

**Answer: B** - Claude accepts base64-encoded images (PNG, JPEG, GIF, WebP) and can analyze their content.

---

### Q29: What is the recommended max_tokens for a detailed analysis response?

A) 10  
B) 100  
C) 1024-4096  
D) 1,000,000  

**Answer: C** - 1024-4096 tokens allows detailed analysis. Adjust based on expected response length.

---

### Q30: Which is the best practice for managing conversation history?

A) Send all messages ever  
B) Summarize older messages and keep only recent ones  
C) Only keep the last message  
D) Never use conversation history  

**Answer: B** - Summarize older turns and keep recent messages to balance context quality and token costs.

---

### Q31: What does a 429 error code mean?

A) Invalid request  
B) Unauthorized  
C) Rate limit exceeded  
D) Server error  

**Answer: C** - 429 Too Many Requests indicates rate limit has been exceeded.

---

### Q32: Which approach best handles multiple tool calls in a single response?

A) Process only the first tool  
B) Execute all tool calls in parallel when possible, then return all results  
C) Ignore additional tool calls  
D) Retry the request  

**Answer: B** - Process all tool calls, parallelize when possible, and return all results together.

---

### Q33: What is the purpose of `input_schema` in a tool definition?

A) To define the tool's name  
B) To specify the expected parameters and their types  
C) To set the output format  
D) To configure caching  

**Answer: B** - `input_schema` defines the expected parameters, types, and which are required.

---

### Q34: When building an agent, why include a "final answer" tool?

A) To increase token usage  
B) To give the agent a clear way to signal task completion with a structured response  
C) It's required by the API  
D) To slow down the agent  

**Answer: B** - A "final_answer" tool gives the agent an explicit way to provide its completed response.

---

### Q35: What is the ANTHROPIC Challenge recognition program?

A) A bug bounty program  
B) A certification and recognition program for developers building with Claude  
C) A coding competition  
D) An open source project  

**Answer: B** - It's a recognition program that awards badges and recognition for demonstrated Claude expertise.

---

### Q36: Which practice reduces hallucination most effectively?

A) Higher temperature  
B) Grounding responses with RAG and asking Claude to cite sources  
C) Longer prompts  
D) Using a smaller model  

**Answer: B** - RAG provides factual context, and asking for citations makes Claude verify against sources.

---

### Q37: What type of content should be cached with `cache_control`?

A) User messages that change every request  
B) Large, stable content like system prompts and reference documents  
C) Output responses  
D) Error messages  

**Answer: B** - Cache stable, large content. Don't cache content that changes frequently.

---

### Q38: How do you version control prompts effectively?

A) Hardcode them in application code  
B) Store in configuration files or a prompt management system with versioning  
C) Keep them in email  
D) Don't version prompts  

**Answer: B** - Use config files or a prompt management system with version control for tracking changes.

---

### Q39: What is the main risk of giving agents unrestricted system access?

A) Higher costs only  
B) Accidental damage, security vulnerabilities, and data loss  
C) Faster performance  
D) Better accuracy  

**Answer: B** - Unrestricted access risks unintended actions, security breaches, and data loss.

---

### Q40: Which combination provides the best production Claude architecture?

A) Direct frontend-to-API with no backend  
B) Backend proxy + RAG + prompt caching + monitoring + error handling  
C) No architecture needed  
D) Only monitoring  

**Answer: B** - A complete production architecture includes backend proxying, RAG, caching, monitoring, and robust error handling.

---

## Scoring

| Score | Assessment |
|-------|------------|
| 36-40 (90-100%) | Exam ready! |
| 32-35 (80-89%) | Almost there |
| 28-31 (70-79%) | Review weak areas |
| <28 (<70%) | More study needed |

---

[Back: Practice Exam 3](./practice-exam-3.md) | [Back to Index](../README.md)
