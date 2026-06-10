# Chapter 04: Introduction to Agent Skills

> **Prerequisites:** Complete [Chapter 03: Claude Code in Action](../03-claude-code-in-action/claude-code-in-action.md) first.

---

[⬅️ Back: Claude Code in Action](../03-claude-code-in-action/claude-code-in-action.md) | [➡️ Next: Introduction to MCP](../05-mcp-intro/introduction-to-mcp.md)

---

## 📋 Overview

Agent skills are the building blocks that allow Claude to perform complex, multi-step tasks autonomously. This chapter covers the fundamentals of agent patterns, tool use, planning, and how to build effective agent systems with Claude.

---

## 1. What Are AI Agents?

### 1.1 Definition

An **AI agent** is a system that can:
1. **Perceive** its environment (through inputs and tools)
2. **Reason** about what to do (through LLM reasoning)
3. **Act** on the environment (through tool execution)
4. **Learn** from results (through feedback loops)

### 1.2 Agents vs. Simple Chatbots

| Aspect | Chatbot | Agent |
|--------|---------|-------|
| Interaction | Single turn | Multi-turn, autonomous |
| Tools | None | Multiple tools available |
| Planning | No planning | Plans and executes steps |
| Error recovery | Fails silently | Retries and adapts |
| Goal orientation | Responds to prompts | Works toward goals |

### 1.3 The Agent Loop

```
┌─────────────┐
│   Receive    │
│    Task      │
└──────┬──────┘
       │
       ▼
┌─────────────┐     ┌──────────────┐
│    Plan      │────▶│   Execute     │
│   Steps      │     │    Action     │
└──────┬──────┘     └──────┬───────┘
       │                    │
       │    ┌───────────────┘
       │    │
       ▼    ▼
┌─────────────┐
│   Observe    │
│   Result     │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   Task       │─── Yes ──▶ Done!
│   Complete?  │
└──────┬──────┘
       │ No
       └──────▶ Back to Plan
```

---

## 2. Core Agent Patterns

### 2.1 ReAct (Reason + Act)

The **ReAct pattern** alternates between reasoning and action:

```python
system_prompt = """
You are a helpful assistant that can use tools to answer questions.

For each step:
1. THINK: Reason about what you need to do
2. ACT: Choose and use an appropriate tool
3. OBSERVE: Process the tool result
4. Repeat until you can answer the question

Always show your reasoning process.
"""
```

**Example execution:**

```
User: What's the population of the capital of France?

THINK: I need to find the capital of France first, then look up its population.
ACT: search("capital of France")
OBSERVE: Paris is the capital of France.
THINK: Now I need to find the population of Paris.
ACT: search("population of Paris")
OBSERVE: Paris has approximately 2.1 million residents (city proper).
THINK: I now have the answer.

Answer: The capital of France is Paris, which has approximately 2.1 million 
residents within the city proper (about 12 million in the greater metropolitan area).
```

### 2.2 Plan-and-Execute

The **Plan-and-Execute** pattern creates a full plan first:

```python
system_prompt = """
You are a task planner. When given a complex task:

1. Break it down into ordered steps
2. Execute each step sequentially
3. If a step fails, revise the plan
4. Continue until the task is complete

Output format:
<plan>
1. Step one
2. Step two
...
</plan>
"""
```

### 2.3 Tool-Using Agent

```python
import anthropic
import json

client = anthropic.Anthropic()

# Define tools
tools = [
    {
        "name": "read_file",
        "description": "Read the contents of a file",
        "input_schema": {
            "type": "object",
            "properties": {
                "path": {"type": "string", "description": "File path"}
            },
            "required": ["path"]
        }
    },
    {
        "name": "write_file",
        "description": "Write content to a file",
        "input_schema": {
            "type": "object",
            "properties": {
                "path": {"type": "string"},
                "content": {"type": "string"}
            },
            "required": ["path", "content"]
        }
    },
    {
        "name": "run_command",
        "description": "Execute a shell command",
        "input_schema": {
            "type": "object",
            "properties": {
                "command": {"type": "string"}
            },
            "required": ["command"]
        }
    }
]

def run_agent(user_task, max_iterations=10):
    """Run an agentic loop to accomplish a task."""
    messages = [{"role": "user", "content": user_task}]
    
    for i in range(max_iterations):
        response = client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=4096,
            system="You are an AI coding agent. Use tools to accomplish tasks.",
            tools=tools,
            messages=messages
        )
        
        # Process response
        messages.append({"role": "assistant", "content": response.content})
        
        # Check if done
        if response.stop_reason == "end_turn":
            return response.content
        
        # Process tool calls
        tool_results = []
        for block in response.content:
            if block.type == "tool_use":
                result = execute_tool(block.name, block.input)
                tool_results.append({
                    "type": "tool_result",
                    "tool_use_id": block.id,
                    "content": result
                })
        
        messages.append({"role": "user", "content": tool_results})
    
    return "Max iterations reached"
```

---

## 3. Agent Skill Components

### 3.1 System Prompts for Agents

```python
agent_system_prompt = """
<role>
You are an expert software engineering agent.
</role>

<capabilities>
- Read and write files
- Execute shell commands
- Search codebases
- Install packages
- Run tests
</capabilities>

<guidelines>
1. Always understand the codebase before making changes
2. Make small, incremental changes
3. Run tests after each change
4. Communicate what you're doing and why
5. Ask for clarification if the task is ambiguous
</guidelines>

<constraints>
- Never delete files without confirmation
- Never modify production configuration
- Always backup before major refactoring
</constraints>
"""
```

### 3.2 Tool Design for Agents

```python
# Good tool design
{
    "name": "search_codebase",
    "description": "Search for patterns in the codebase using regex. Returns matching file paths and line numbers.",
    "input_schema": {
        "type": "object",
        "properties": {
            "pattern": {
                "type": "string",
                "description": "Regex pattern to search for"
            },
            "file_type": {
                "type": "string",
                "description": "File extension filter (e.g., 'py', 'js')",
                "enum": ["py", "js", "ts", "java", "go", "all"]
            },
            "directory": {
                "type": "string",
                "description": "Directory to search in (default: project root)"
            }
        },
        "required": ["pattern"]
    }
}

# Poor tool design (too vague)
{
    "name": "do_stuff",
    "description": "Does things with code",
    "input_schema": {
        "type": "object",
        "properties": {
            "input": {"type": "string"}
        }
    }
}
```

**Tool Design Principles:**
1. **Descriptive names** — `search_codebase` not `search`
2. **Clear descriptions** — Explain what the tool does and when to use it
3. **Typed parameters** — Use proper JSON Schema types
4. **Required vs optional** — Mark only truly required fields
5. **Enums** — Use enums for parameters with fixed options

### 3.3 Agent Memory and Context

```python
class AgentMemory:
    """Simple agent memory for maintaining context."""
    
    def __init__(self):
        self.conversation_history = []
        self.task_log = []
        self.findings = {}
    
    def add_message(self, role, content):
        self.conversation_history.append({"role": role, "content": content})
    
    def log_action(self, action, result):
        self.task_log.append({"action": action, "result": result})
    
    def save_finding(self, key, value):
        self.findings[key] = value
    
    def get_context_summary(self):
        """Summarize context for inclusion in prompts."""
        return f"""
<task_log>
{json.dumps(self.task_log[-5:], indent=2)}
</task_log>

<findings>
{json.dumps(self.findings, indent=2)}
</findings>
"""
```

---

## 4. Multi-Agent Systems

### 4.1 Orchestrator Pattern

```python
orchestrator_prompt = """
You are an orchestrator agent. Your job is to:
1. Receive a complex task
2. Break it into subtasks
3. Delegate each subtask to the appropriate specialist
4. Synthesize the results

Available specialists:
- code_analyst: Analyzes code quality and architecture
- security_auditor: Identifies security vulnerabilities
- test_engineer: Writes and runs tests
- documentation_writer: Creates documentation
"""
```

### 4.2 Specialist Agents

```python
specialists = {
    "code_analyst": {
        "system": "You are a code analysis specialist. Analyze code for quality, patterns, and improvements.",
        "tools": ["read_file", "search_codebase", "analyze_complexity"]
    },
    "security_auditor": {
        "system": "You are a security specialist. Identify vulnerabilities following OWASP guidelines.",
        "tools": ["read_file", "search_codebase", "check_dependencies"]
    },
    "test_engineer": {
        "system": "You are a testing specialist. Write comprehensive tests for the given code.",
        "tools": ["read_file", "write_file", "run_tests"]
    }
}
```

### 4.3 Agent Communication

```python
def orchestrate(task, specialists):
    """Orchestrate a task across multiple specialist agents."""
    
    # Step 1: Planner analyzes the task
    plan = call_agent("planner", f"Create a plan for: {task}")
    
    # Step 2: Execute each step with the right specialist
    results = {}
    for step in plan.steps:
        specialist = step.specialist
        result = call_agent(specialist, step.prompt)
        results[step.id] = result
    
    # Step 3: Synthesize results
    synthesis = call_agent("synthesizer", f"""
    Combine these results into a final answer:
    {json.dumps(results)}
    """)
    
    return synthesis
```

---

## 5. Error Handling in Agents

### 5.1 Graceful Degradation

```python
def robust_agent_tool_call(tool_name, tool_input, max_retries=3):
    """Execute a tool call with retry logic."""
    for attempt in range(max_retries):
        try:
            result = execute_tool(tool_name, tool_input)
            return {"success": True, "result": result}
        except Exception as e:
            if attempt == max_retries - 1:
                return {"success": False, "error": str(e)}
            time.sleep(2 ** attempt)
    return {"success": False, "error": "Max retries exceeded"}
```

### 5.2 Self-Correction

```python
self_correction_prompt = """
If a tool call fails or produces unexpected results:
1. Analyze the error message
2. Determine if the error is recoverable
3. If recoverable, adjust your approach and try again
4. If not recoverable, report the issue to the user

Common recovery strategies:
- File not found: Search for the correct path
- Command failed: Check the syntax and dependencies
- API error: Verify parameters and retry
"""
```

---

## 6. Guardrails and Safety

### 6.1 Output Validation

```python
def validate_agent_output(output, schema):
    """Validate agent output against expected schema."""
    try:
        parsed = json.loads(output)
        jsonschema.validate(parsed, schema)
        return True, parsed
    except json.JSONDecodeError:
        return False, "Output is not valid JSON"
    except jsonschema.ValidationError as e:
        return False, f"Output doesn't match schema: {e.message}"
```

### 6.2 Action Limits

```python
class SafeAgent:
    """Agent with safety guardrails."""
    
    ALLOWED_COMMANDS = ["ls", "cat", "grep", "npm test", "python"]
    MAX_FILE_SIZE = 1_000_000  # 1MB
    MAX_ITERATIONS = 20
    
    def __init__(self):
        self.iteration_count = 0
        self.actions_taken = []
    
    def validate_command(self, command):
        """Ensure command is safe to execute."""
        base_cmd = command.split()[0]
        if base_cmd not in self.ALLOWED_COMMANDS:
            raise ValueError(f"Command '{base_cmd}' is not allowed")
        return True
    
    def check_iteration_limit(self):
        """Prevent infinite loops."""
        self.iteration_count += 1
        if self.iteration_count > self.MAX_ITERATIONS:
            raise RuntimeError("Max iterations reached")
```

---

## 7. Practical Example: Building a Code Review Agent

```python
import anthropic
import json

client = anthropic.Anthropic()

CODE_REVIEW_TOOLS = [
    {
        "name": "read_file",
        "description": "Read file contents",
        "input_schema": {
            "type": "object",
            "properties": {
                "path": {"type": "string"}
            },
            "required": ["path"]
        }
    },
    {
        "name": "search_pattern",
        "description": "Search codebase for a pattern",
        "input_schema": {
            "type": "object",
            "properties": {
                "pattern": {"type": "string"},
                "directory": {"type": "string"}
            },
            "required": ["pattern"]
        }
    }
]

def review_code(file_path):
    """Run an agentic code review."""
    
    messages = [{
        "role": "user",
        "content": f"""
Review the code in {file_path} for:

1. **Security vulnerabilities** — SQL injection, XSS, auth issues
2. **Performance issues** — N+1 queries, memory leaks, unnecessary operations
3. **Code quality** — Naming, structure, DRY violations
4. **Error handling** — Missing error cases, silent failures
5. **Best practices** — Language-specific patterns and conventions

Provide specific, actionable feedback with line references.
"""
    }]
    
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=4096,
        system="""You are a senior code reviewer. Use tools to read and analyze code.
Provide specific, actionable feedback. Rate severity as: 🔴 Critical, 🟡 Warning, 🔵 Info.""",
        tools=CODE_REVIEW_TOOLS,
        messages=messages
    )
    
    return response
```

---

## 🧪 Mini-Quiz: Agent Skills

**Q1:** What is the ReAct pattern?

<details>
<summary>Show Answer</summary>

ReAct stands for **Reason + Act**. It's an agent pattern where the LLM alternates between reasoning about what to do and taking actions using tools, observing results, and iterating until the task is complete.
</details>

**Q2:** What are the key principles of good tool design for agents?

<details>
<summary>Show Answer</summary>

1. Descriptive names (`search_codebase` not `search`)
2. Clear descriptions explaining what the tool does and when to use it
3. Proper JSON Schema types for parameters
4. Distinguishing required vs optional parameters
5. Using enums for fixed-value parameters
</details>

**Q3:** How does the orchestrator pattern work in multi-agent systems?

<details>
<summary>Show Answer</summary>

A **central orchestrator agent** receives the task, breaks it into subtasks, delegates each to the appropriate specialist agent, and then synthesizes the results into a final answer. Each specialist has its own system prompt and tool set.
</details>

---

## 🔑 Key Takeaways

1. **Agents** are systems that perceive, reason, act, and learn autonomously
2. **Core patterns**: ReAct (reason+act), Plan-and-Execute, Tool-Using Agent
3. **Tool design** is critical — good descriptions help Claude choose the right tool
4. **Multi-agent systems** use orchestrators and specialists for complex tasks
5. **Guardrails** (iteration limits, command allowlists, output validation) are essential
6. **Self-correction** enables agents to recover from errors autonomously

---

[⬅️ Back: Claude Code in Action](../03-claude-code-in-action/claude-code-in-action.md) | [➡️ Next: Introduction to MCP](../05-mcp-intro/introduction-to-mcp.md)
