# 🔧 Hands-on Exercises

> Practice exercises to reinforce your learning — beginner to intermediate level.

---

[⬅️ Back to Index](../README.md)

---

## Exercise 01: Hello Claude (Beginner)

**Objective:** Make your first API call to Claude.

**Task:**
1. Set up your environment (API key, SDK)
2. Write a script that sends "Hello, Claude!" and prints the response
3. Print the token usage

**Expected Output:**
```
Response: Hello! I'm Claude, an AI assistant made by Anthropic. How can I help you today?
Input tokens: 10
Output tokens: 18
```

**Starter Code:**

```python
import anthropic

# TODO: Create the client
client = # ???

# TODO: Send a message
response = # ???

# TODO: Print the response and usage
print(f"Response: {???}")
print(f"Input tokens: {???}")
print(f"Output tokens: {???}")
```

**Solution:**

<details>
<summary>Click to reveal</summary>

```python
import anthropic

client = anthropic.Anthropic()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=100,
    messages=[
        {"role": "user", "content": "Hello, Claude!"}
    ]
)

print(f"Response: {response.content[0].text}")
print(f"Input tokens: {response.usage.input_tokens}")
print(f"Output tokens: {response.usage.output_tokens}")
```

</details>

---

## Exercise 02: System Prompt Mastery (Beginner)

**Objective:** Use system prompts to control Claude's behavior.

**Task:**
1. Create a system prompt that makes Claude respond only in haiku format (5-7-5 syllables)
2. Test with 3 different questions
3. Verify the responses follow the haiku format

**Starter Code:**

```python
import anthropic

client = anthropic.Anthropic()

# TODO: Create a system prompt that forces haiku responses
system_prompt = # ???

questions = [
    "What is machine learning?",
    "Explain recursion",
    "What is the internet?"
]

for question in questions:
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=200,
        system=???,
        messages=[{"role": "user", "content": question}]
    )
    print(f"Q: {question}")
    print(f"A: {response.content[0].text}\n")
```

**Solution:**

<details>
<summary>Click to reveal</summary>

```python
import anthropic

client = anthropic.Anthropic()

system_prompt = """You are a poetic AI assistant. You must respond to EVERY question 
in the form of a haiku (3 lines: 5 syllables, 7 syllables, 5 syllables). 
No additional text, explanations, or formatting. Just the haiku."""

questions = [
    "What is machine learning?",
    "Explain recursion",
    "What is the internet?"
]

for question in questions:
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=200,
        system=system_prompt,
        messages=[{"role": "user", "content": question}]
    )
    print(f"Q: {question}")
    print(f"A: {response.content[0].text}\n")
```

</details>

---

## Exercise 03: Multi-turn Conversation (Beginner)

**Objective:** Implement a multi-turn conversation with Claude.

**Task:**
1. Create a conversation loop that maintains history
2. Allow the user to type messages
3. Keep the last 10 messages for context
4. Add a "quit" command to exit

**Starter Code:**

```python
import anthropic

client = anthropic.Anthropic()
conversation_history = []

print("Chat with Claude! Type 'quit' to exit.\n")

while True:
    user_input = input("You: ")
    
    if user_input.lower() == 'quit':
        break
    
    # TODO: Add user message to history
    # ???
    
    # TODO: Call Claude with full conversation history
    # ???
    
    # TODO: Add assistant response to history
    # ???
    
    # TODO: Trim history to last 10 messages
    # ???
    
    print(f"Claude: {???}\n")
```

**Solution:**

<details>
<summary>Click to reveal</summary>

```python
import anthropic

client = anthropic.Anthropic()
conversation_history = []

print("Chat with Claude! Type 'quit' to exit.\n")

while True:
    user_input = input("You: ")
    
    if user_input.lower() == 'quit':
        break
    
    conversation_history.append({"role": "user", "content": user_input})
    
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=1024,
        messages=conversation_history
    )
    
    assistant_message = response.content[0].text
    conversation_history.append({"role": "assistant", "content": assistant_message})
    
    # Keep last 10 messages
    conversation_history = conversation_history[-10:]
    
    print(f"Claude: {assistant_message}\n")
```

</details>

---

## Exercise 04: Tool Use Implementation (Intermediate)

**Objective:** Implement a tool use flow with the Claude API.

**Task:**
1. Define a calculator tool (add, subtract, multiply, divide)
2. Send a math problem to Claude
3. Handle the tool call
4. Return the result

**Starter Code:**

```python
import anthropic
import json

client = anthropic.Anthropic()

# TODO: Define the calculator tool
tools = [???]

def calculator(operation: str, a: float, b: float) -> float:
    """Simple calculator."""
    operations = {
        "add": lambda x, y: x + y,
        "subtract": lambda x, y: x - y,
        "multiply": lambda x, y: x * y,
        "divide": lambda x, y: x / y if y != 0 else "Error: Division by zero"
    }
    return operations[operation](a, b)

# TODO: Send message with tools
response = ???

# TODO: Check if Claude wants to use the tool
# TODO: Execute the tool and return the result
# TODO: Print the final answer
```

**Solution:**

<details>
<summary>Click to reveal</summary>

```python
import anthropic
import json

client = anthropic.Anthropic()

tools = [
    {
        "name": "calculator",
        "description": "Perform a mathematical calculation",
        "input_schema": {
            "type": "object",
            "properties": {
                "operation": {
                    "type": "string",
                    "enum": ["add", "subtract", "multiply", "divide"],
                    "description": "The operation to perform"
                },
                "a": {"type": "number", "description": "First operand"},
                "b": {"type": "number", "description": "Second operand"}
            },
            "required": ["operation", "a", "b"]
        }
    }
]

def calculator(operation: str, a: float, b: float) -> float:
    operations = {
        "add": lambda x, y: x + y,
        "subtract": lambda x, y: x - y,
        "multiply": lambda x, y: x * y,
        "divide": lambda x, y: x / y if y != 0 else "Error: Division by zero"
    }
    return operations[operation](a, b)

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    tools=tools,
    messages=[{"role": "user", "content": "What is 347 multiplied by 29?"}]
)

if response.stop_reason == "tool_use":
    for block in response.content:
        if block.type == "tool_use":
            result = calculator(**block.input)
            
            response = client.messages.create(
                model="claude-sonnet-4-20250514",
                max_tokens=1024,
                tools=tools,
                messages=[
                    {"role": "user", "content": "What is 347 multiplied by 29?"},
                    {"role": "assistant", "content": response.content},
                    {"role": "user", "content": [{
                        "type": "tool_result",
                        "tool_use_id": block.id,
                        "content": str(result)
                    }]}
                ]
            )

print(response.content[0].text)
```

</details>

---

## Exercise 05: Streaming Chat (Intermediate)

**Objective:** Implement a streaming chat interface.

**Task:**
1. Use the streaming API to display responses in real-time
2. Show a typing indicator while streaming
3. Count tokens as they stream

**Starter Code:**

```python
import anthropic

client = anthropic.Anthropic()

def streaming_chat(message, history=[]):
    # TODO: Use client.messages.stream()
    # TODO: Print each chunk as it arrives
    # TODO: Track token usage
    pass

# Test it
streaming_chat("Explain how neural networks work in simple terms.")
```

**Solution:**

<details>
<summary>Click to reveal</summary>

```python
import anthropic

client = anthropic.Anthropic()

def streaming_chat(message, history=[]):
    history.append({"role": "user", "content": message})
    
    print("Claude: ", end="", flush=True)
    
    with client.messages.stream(
        model="claude-sonnet-4-20250514",
        max_tokens=1024,
        messages=history
    ) as stream:
        full_response = ""
        for text in stream.text_stream:
            print(text, end="", flush=True)
            full_response += text
    
    print("\n")
    history.append({"role": "assistant", "content": full_response})
    return full_response

# Test
streaming_chat("Explain how neural networks work in simple terms.")
```

</details>

---

## Exercise 06: Build an MCP Server (Intermediate)

**Objective:** Create a simple MCP server with custom tools.

**Task:**
1. Create an MCP server with two tools:
   - `string_reverse`: Reverse a string
   - `word_count`: Count words in text
2. Add a resource that returns server info
3. Test the server locally

**Starter Code:**

```typescript
// string-tools-server.ts
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
    name: "string-tools",
    version: "1.0.0"
});

// TODO: Add string_reverse tool
// server.tool(...)

// TODO: Add word_count tool
// server.tool(...)

// TODO: Add server info resource
// server.resource(...)

// Start server
const transport = new StdioServerTransport();
await server.connect(transport);
```

**Solution:**

<details>
<summary>Click to reveal</summary>

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
    name: "string-tools",
    version: "1.0.0"
});

server.tool(
    "string_reverse",
    "Reverse a string",
    { input: z.string().describe("String to reverse") },
    async ({ input }) => ({
        content: [{
            type: "text",
            text: input.split("").reverse().join("")
        }]
    })
);

server.tool(
    "word_count",
    "Count words in text",
    { text: z.string().describe("Text to count words in") },
    async ({ text }) => ({
        content: [{
            type: "text",
            text: JSON.stringify({
                words: text.split(/\s+/).filter(w => w.length > 0).length,
                characters: text.length,
                characters_no_spaces: text.replace(/\s/g, "").length
            })
        }]
    })
);

server.resource(
    "server-info",
    "info://server",
    async (uri) => ({
        contents: [{
            uri: uri.href,
            text: JSON.stringify({
                name: "string-tools",
                version: "1.0.0",
                tools: ["string_reverse", "word_count"]
            }, null, 2)
        }]
    })
);

const transport = new StdioServerTransport();
await server.connect(transport);
```

</details>

---

## Exercise 07: Building an Agent (Advanced)

**Objective:** Build a simple research agent that can search and summarize.

**Task:**
1. Create an agent with tools for "search" (simulated) and "summarize"
2. Implement the agent loop
3. Handle multi-step reasoning
4. Set a maximum iteration limit

**Starter Code:**

```python
import anthropic
import json

client = anthropic.Anthropic()

# Simulated search tool
def mock_search(query):
    database = {
        "python": "Python is a high-level programming language...",
        "claude": "Claude is an AI assistant by Anthropic...",
        "mcp": "MCP is the Model Context Protocol..."
    }
    results = [(k, v) for k, v in database.items() if k in query.lower()]
    return results if results else [("general", "No specific results found.")]

# TODO: Define tools for the agent
tools = [???]

# TODO: Implement the agent loop
def run_agent(task, max_iterations=5):
    pass

# Test
result = run_agent("Tell me about Python and Claude")
print(result)
```

**Solution:**

<details>
<summary>Click to reveal</summary>

```python
import anthropic
import json

client = anthropic.Anthropic()

def mock_search(query):
    database = {
        "python": "Python is a high-level programming language created by Guido van Rossum. It supports multiple paradigms and has a vast ecosystem.",
        "claude": "Claude is a family of AI models developed by Anthropic, designed to be helpful, harmless, and honest.",
        "mcp": "MCP (Model Context Protocol) is an open protocol that standardizes how AI models interact with tools and data sources."
    }
    results = [(k, v) for k, v in database.items() if k in query.lower()]
    return results if results else [("general", "No specific results found.")]

tools = [
    {
        "name": "search",
        "description": "Search for information about a topic",
        "input_schema": {
            "type": "object",
            "properties": {
                "query": {"type": "string", "description": "Search query"}
            },
            "required": ["query"]
        }
    }
]

def execute_tool(name, input_data):
    if name == "search":
        results = mock_search(input_data["query"])
        return json.dumps(results)
    return "Unknown tool"

def run_agent(task, max_iterations=5):
    messages = [{"role": "user", "content": task}]
    
    for i in range(max_iterations):
        response = client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=2048,
            system="You are a research agent. Use the search tool to find information, then synthesize a comprehensive answer.",
            tools=tools,
            messages=messages
        )
        
        messages.append({"role": "assistant", "content": response.content})
        
        if response.stop_reason == "end_turn":
            # Extract text response
            for block in response.content:
                if block.type == "text":
                    return block.text
        
        # Handle tool calls
        tool_results = []
        for block in response.content:
            if block.type == "tool_use":
                result = execute_tool(block.name, block.input)
                tool_results.append({
                    "type": "tool_result",
                    "tool_use_id": block.id,
                    "content": result
                })
        
        if tool_results:
            messages.append({"role": "user", "content": tool_results})
    
    return "Agent reached maximum iterations"

result = run_agent("Tell me about Python and Claude")
print(result)
```

</details>

---

## Exercise 08: Error Handling & Retries (Intermediate)

**Objective:** Implement robust error handling for API calls.

**Task:**
1. Implement exponential backoff for rate limits
2. Handle different error types appropriately
3. Add logging for all errors
4. Test with simulated failures

**Starter Code:**

```python
import anthropic
import time
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def robust_api_call(messages, max_retries=3, **kwargs):
    """Make an API call with retry logic."""
    # TODO: Implement retry logic with exponential backoff
    # TODO: Handle specific error types
    # TODO: Log all attempts and errors
    pass
```

**Solution:**

<details>
<summary>Click to reveal</summary>

```python
import anthropic
import time
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def robust_api_call(messages, max_retries=3, **kwargs):
    defaults = {
        "model": "claude-sonnet-4-20250514",
        "max_tokens": 1024
    }
    defaults.update(kwargs)
    
    last_error = None
    
    for attempt in range(max_retries):
        try:
            logger.info(f"API call attempt {attempt + 1}/{max_retries}")
            
            client = anthropic.Anthropic()
            response = client.messages.create(messages=messages, **defaults)
            
            logger.info(f"Success! Tokens: {response.usage.input_tokens}in/{response.usage.output_tokens}out")
            return response
            
        except anthropic.RateLimitError as e:
            wait_time = 2 ** attempt
            logger.warning(f"Rate limited. Waiting {wait_time}s...")
            time.sleep(wait_time)
            last_error = e
            
        except anthropic.APIConnectionError as e:
            wait_time = 2 ** attempt
            logger.warning(f"Connection error. Retrying in {wait_time}s...")
            time.sleep(wait_time)
            last_error = e
            
        except anthropic.BadRequestError as e:
            logger.error(f"Bad request (no retry): {e}")
            raise
            
        except anthropic.AuthenticationError as e:
            logger.error(f"Authentication failed (no retry): {e}")
            raise
            
        except anthropic.APIStatusError as e:
            logger.error(f"API error {e.status_code}: {e.message}")
            if e.status_code >= 500:
                wait_time = 2 ** attempt
                time.sleep(wait_time)
                last_error = e
            else:
                raise
    
    raise Exception(f"Failed after {max_retries} retries. Last error: {last_error}")
```

</details>

---

## Exercise 09: Prompt Engineering Challenge (Beginner-Intermediate)

**Objective:** Craft effective prompts for different use cases.

**Task:** Write system prompts for each scenario that consistently produce the desired output:

1. **JSON API Responder** — Always returns valid JSON
2. **Code Reviewer** — Reviews code with specific severity ratings
3. **Data Extractor** — Extracts structured data from unstructured text

**For each, write:**
- The system prompt
- A test message
- The expected output format

**Solution:**

<details>
<summary>Click to reveal</summary>

```python
import anthropic
import json

client = anthropic.Anthropic()

# 1. JSON API Responder
json_prompt = """You are a JSON API. You must ALWAYS respond with valid JSON only.
No markdown, no explanations, no extra text. Just the JSON object.

Schema for responses:
{
  "status": "success" | "error",
  "data": any,
  "message": string (optional, for errors)
}"""

# Test
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=256,
    system=json_prompt,
    messages=[{"role": "user", "content": "List 3 programming languages"}]
)
print("JSON Response:", response.content[0].text)
assert json.loads(response.content[0].text)  # Verify valid JSON

# 2. Code Reviewer
reviewer_prompt = """You are a senior code reviewer. Analyze code and report issues.

For each issue found, use this format:
- 🔴 [CRITICAL] Line X: Description
- 🟡 [WARNING] Line X: Description  
- 🔵 [INFO] Line X: Description

Categories: Security, Performance, Readability, Best Practices, Error Handling

End with a summary: "Found X critical, Y warnings, Z info issues."
"""

# 3. Data Extractor
extractor_prompt = """You are a data extraction assistant. Extract structured data from text.

Rules:
- Extract only explicitly stated information
- Use null for missing fields
- Use ISO 8601 for dates
- Use lowercase for enums
- Return valid JSON only

Output format:
{
  "entities": [{"name": string, "type": string}],
  "dates": [{"value": string, "context": string}],
  "numbers": [{"value": number, "context": string}],
  "locations": [{"name": string, "type": "city|country|address"}]
}"""
```

</details>

---

## Exercise 10: Cost Calculator (Beginner)

**Objective:** Calculate and optimize API costs.

**Task:**
1. Build a cost calculator that tracks spending across multiple API calls
2. Show cost breakdown by model
3. Suggest optimizations

**Starter Code:**

```python
class CostCalculator:
    PRICING = {
        "claude-opus-4-20250514": {"input": 0.015, "output": 0.075},
        "claude-sonnet-4-20250514": {"input": 0.003, "output": 0.015},
        "claude-haiku-3-5-20241022": {"input": 0.001, "output": 0.005}
    }
    
    def __init__(self):
        self.usage_log = []
    
    # TODO: Implement track_usage method
    # TODO: Implement get_total_cost method
    # TODO: Implement get_cost_by_model method
    # TODO: Implement suggest_optimizations method
    pass
```

**Solution:**

<details>
<summary>Click to reveal</summary>

```python
class CostCalculator:
    PRICING = {
        "claude-opus-4-20250514": {"input": 0.015, "output": 0.075},
        "claude-sonnet-4-20250514": {"input": 0.003, "output": 0.015},
        "claude-haiku-3-5-20241022": {"input": 0.001, "output": 0.005}
    }
    
    def __init__(self):
        self.usage_log = []
    
    def track_usage(self, model, input_tokens, output_tokens):
        cost = self.calculate_cost(model, input_tokens, output_tokens)
        self.usage_log.append({
            "model": model,
            "input_tokens": input_tokens,
            "output_tokens": output_tokens,
            "cost": cost
        })
        return cost
    
    def calculate_cost(self, model, input_tokens, output_tokens):
        pricing = self.PRICING.get(model)
        if not pricing:
            return 0
        return (input_tokens / 1000 * pricing["input"] + 
                output_tokens / 1000 * pricing["output"])
    
    def get_total_cost(self):
        return sum(entry["cost"] for entry in self.usage_log)
    
    def get_cost_by_model(self):
        by_model = {}
        for entry in self.usage_log:
            model = entry["model"]
            by_model[model] = by_model.get(model, 0) + entry["cost"]
        return by_model
    
    def suggest_optimizations(self):
        suggestions = []
        total = self.get_total_cost()
        
        by_model = self.get_cost_by_model()
        if "claude-opus-4-20250514" in by_model:
            opus_pct = by_model["claude-opus-4-20250514"] / total * 100
            if opus_pct > 50:
                suggestions.append(f"⚠️ {opus_pct:.0f}% of costs are from Opus. Consider using Sonnet for simpler tasks.")
        
        total_tokens = sum(e["input_tokens"] + e["output_tokens"] for e in self.usage_log)
        if total_tokens > 100000:
            suggestions.append("💡 High token usage. Consider implementing prompt caching or conversation summarization.")
        
        avg_output = sum(e["output_tokens"] for e in self.usage_log) / len(self.usage_log)
        if avg_output > 2000:
            suggestions.append("💡 High average output tokens. Consider setting lower max_tokens or being more specific in prompts.")
        
        return suggestions

# Test
calc = CostCalculator()
calc.track_usage("claude-opus-4-20250514", 1000, 500)
calc.track_usage("claude-sonnet-4-20250514", 2000, 1000)
calc.track_usage("claude-haiku-3-5-20241022", 5000, 2000)

print(f"Total cost: ${calc.get_total_cost():.4f}")
print(f"By model: {calc.get_cost_by_model()}")
for suggestion in calc.suggest_optimizations():
    print(suggestion)
```

</details>

---

[⬅️ Back to Index](../README.md)
