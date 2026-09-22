
Introduction to AI Fluency
 The AI Fluency Framework
Why do we need AI Fluency?
The 4D Framework
 Deep Dive 1: What is Generative AI?
Generative AI fundamentals
Capabilities & limitations
 Delegation
A closer look at Delegation
Project planning and Delegation
 Description
A closer look at Description
 Deep Dive 2: Effective prompting techniques
Effective prompting techniques
 Discernment
A closer look at Discernment
 The Description-Discernment loop
The Description-Discernment loop
 Diligence

Course Overview
This comprehensive video course teaches developers how to integrate Claude AI into applications using the Anthropic API. The curriculum covers fundamental API operations, advanced prompting techniques, tool integration, and architectural patterns for building AI-powered systems. Through hands-on exercises and practical examples, participants will learn to implement conversational AI, retrieval-augmented generation, automated workflows, and leverage Claude's multimodal capabilities for processing text, images, and documents.

What You'll Learn
Set up and authenticate with the Anthropic API, including API key management and request configuration
Implement single and multi-turn conversations with proper message formatting and context handling
Configure system prompts and control model behavior using temperature, response streaming, and structured output formats
Design and execute prompt evaluation workflows with test dataset generation and automated grading systems
Apply prompt engineering techniques including XML tag structuring, example-based learning, and clear directive formulation
Integrate Claude's tool use capabilities to extend functionality with custom tools, batch operations, and web search
Build retrieval-augmented generation (RAG) systems with text chunking, embeddings, BM25 search, and contextual retrieval
Utilize Claude's extended features including extended thinking mode, image analysis, PDF processing, and citation generation
Implement prompt caching strategies to optimize API usage and reduce latency
Develop Model Context Protocol (MCP) servers and clients for standardized tool and resource integration
Deploy Anthropic Apps including Claude Code for automated development tasks and Computer Use for UI automation
Architect agent-based systems with parallelization, chaining, and routing workflows
Prerequisites
Proficiency in Python programming
Basic knowledge of handling JSON data
Who This Course Is For
Backend developers building AI-powered APIs and services
Full-stack engineers integrating conversational AI into web applications
Data engineers implementing document processing and knowledge retrieval systems
DevOps professionals automating workflows with AI assistance
Technical architects designing scalable AI-integrated systems
Software engineers transitioning to AI/ML application development
Developers working on chatbots, virtual assistants, or content generation tools

  Introduction 
Welcome to the course
 Anthropic overview 
Overview of Claude models
 Accessing Claude with the API 
Accessing the API
Getting an API key
Making a request
Multi-Turn conversations
Chat exercise
System prompts
System prompts exercise
Temperature
Course satisfaction survey
Response streaming
Structured data
Structured data exercise
Quiz on accessing Claude with the API
 Prompt evaluation 
Prompt evaluation
A typical eval workflow
Generating test datasets
Running the eval
Model based grading
Code based grading
Exercise on prompt evals
Quiz on prompt evaluation
 Prompt engineering techniques 
Prompt engineering
Being clear and direct
Being specific
Structure with XML tags
Providing examples
Exercise on prompting
Quiz on prompt engineering techniques
 Tool use with Claude 
Introducing tool use
Project overview
Tool functions
Tool schemas
Handling message blocks
Sending tool results
Multi-turn conversations with tools
Implementing multiple turns
Using multiple tools
Fine grained tool calling
The text edit tool
The web search tool
Quiz on tool use with Claude
 RAG and Agentic Search 
Introducing Retrieval Augmented Generation
Text chunking strategies
Text embeddings
The full RAG flow
Implementing the RAG flow
BM25 lexical search
A Multi-Index RAG pipeline
 Features of Claude 
Extended thinking
Image support
PDF support
Citations
Prompt caching
Rules of prompt caching
Prompt caching in action
Code execution and the Files API
Quiz on features of Claude
 Model Context Protocol 
Introducing MCP
MCP clients
Project setup
Defining tools with MCP
The server inspector
Implementing a client
Defining resources
Accessing resources
Defining prompts
Prompts in the client
MCP review
Quiz on Model Context Protocol
 Anthropic apps - Claude Code and computer use 
Anthropic apps
Claude Code setup
Claude Code in action
Enhancements with MCP servers
 Agents and workflows 
Agents and workflows
Parallelization workflows
Chaining workflows
Routing workflows
Agents and tools
Environment inspection
Workflows vs agents

Course Overview
 Steer the Work 
Steering Long Sessions
 Configure Claude 
A CLAUDE.md That Follows
Verification Skills
Permission Modes
Hooks
 Automate Repeat Work 
Routines and Headless
GitHub Actions and Code Review
 Verify and Share 
Trust It: Verifying Unsupervised Runs
Plugins

In this course, you'll learn how to stop repeating yourself and start teaching Claude once. You'll discover what Skills are and how they differ from other Claude Code customization options like CLAUDE.md, hooks, and subagents. You'll create your first Skill from scratch — writing the SKILL.md frontmatter, crafting effective descriptions that reliably trigger matching, and organizing your skill directory with progressive disclosure to keep context windows efficient. You'll also explore advanced configuration options like restricting tool access with allowed-tools and using scripts that execute without consuming context.

Beyond building individual Skills, you'll learn how to share them with your team by committing them to a repository, distribute them more broadly through plugins, and deploy them organization-wide using enterprise managed settings. You'll see how to wire Skills into custom subagents for isolated, expert task delegation, and you'll walk through a complete troubleshooting guide for diagnosing issues — from skills that won't trigger to priority conflicts and runtime errors. By the end, you'll have the knowledge to build a full Skills-based workflow that keeps Claude consistent, context-efficient, and aligned with your team's standards.

Curriculum
About this course
About this course
This course provides comprehensive coverage of the Model Context Protocol (MCP), focusing on building both MCP servers and clients using the Python SDK. You'll learn about MCP's three core primitives—tools, resources, and prompts—and understand how they integrate with Claude AI to create powerful applications without writing extensive integration code.

What you'll learn
Understand MCP architecture and how it shifts tool definition and execution burden from your server to specialized MCP servers
Learn about MCP's transport-agnostic communication system and the message types used between clients and servers
Explore the complete request-response flow from user queries through MCP clients to external services and back to Claude
Build MCP servers using the Python SDK with decorators to define tools instead of writing JSON schemas manually
Implement document management functionality with tools for reading and editing documents using Field descriptions and type hints
Use the built-in MCP Server Inspector to test and debug your server functionality in a browser-based interface
Define resources for exposing read-only data, including both direct resources with static URIs and templated resources with parameters
Implement resource reading functionality in clients with proper MIME type handling for JSON and text content
Build prompts that provide pre-crafted, high-quality instructions for common workflows like document formatting
Understand when to use each MCP primitive: tools (model-controlled), resources (app-controlled), and prompts (user-controlled)
Examine practical integration patterns including autocomplete functionality and context injection for AI conversations
Prerequisites
Working knowledge of Python programming
Basic understanding of JSON and HTTP request-response patterns
Who this course is for
Developers looking to create MCP servers 

Curriculum
About this course
About this course
This course examines advanced features and implementation patterns for Model Context Protocol (MCP) development, focusing on server-client communication, transport mechanisms, and production deployment considerations. You'll explore sophisticated MCP capabilities including sampling for AI model integration, notification systems, file system access control, and the technical details of different transport protocols.

What you'll learn
Sampling implementation - Understand how MCP servers can request language model calls through connected clients, including the architecture that shifts AI costs and complexity from server to client
Progress and logging notifications - Learn to implement real-time feedback systems using context objects, logging callbacks, and progress reporting for long-running operations
Roots-based file access - Explore permission systems that grant MCP servers access to specific directories while providing security boundaries and enabling user-friendly file discovery
JSON message architecture - Examine the complete MCP message specification, distinguishing between request-result pairs and notification messages, and understanding bidirectional communication patterns
Stdio transport mechanisms - Understand how MCP clients and servers communicate through standard input/output streams, including the required initialization handshake sequence
StreamableHTTP transport implementation - Learn how Server-Sent Events (SSE) enable server-to-client communication over HTTP, including session management and dual-connection architectures
HTTP transport limitations - Discover how configuration flags affect functionality, particularly regarding server-initiated requests and streaming capabilities
Production scaling considerations - Understand when to use stateless HTTP for horizontal scaling with load balancers and the trade-offs between stateful and stateless server configurations
Transport selection criteria - Learn to choose appropriate transport methods based on deployment requirements, functionality needs, and scaling constraints
Prerequisites
Experience with Python development and async programming patterns
Familiarity with JSON message formats and HTTP protocols
Basic knowledge of Server-Sent Events (SSE)
Who this course is for
Developers working with Model Context Protocol implementations
Engineers building MCP servers and clients

Detailed objectives by domain
Each domain below lists the skills against which exam items are written. Skill descriptions summarize
the knowledge and competencies measured. Skill weights show each skill's share of the overall exam.
Domain 1: Agents and Workflows (14.7%)
Agent Architecture (4.5%)
Principles, patterns, and tradeoffs of agent and workflow architecture, including the decision criteria
for using a workflow versus an agent, the structure of manager/supervisor hierarchies, and the role of
subagents in improving task execution.
Agent Construction with Claude (5.3%)
Methods, tools, and platforms for constructing Claude agents, including the Claude Agent SDK,

custom agent loops and harnesses, managed agent deployment models (self-hosted vs. Anthropic-
hosted), and hooks for deterministic actions.

Agent Patterns and Frameworks (4.9%)
Common agent design patterns (tool-use loops, sub-agents, memory, context-window management)
and agentic abstraction frameworks (e.g., Strands, LangGraph, PydanticAI) for building agents and
workflows for multi-step tasks.
Claude Certification Program Exam guide

Domain 2: Applications and Integration (33.1%)
Understanding Requirements (3.4%)
Functional and infrastructure requirements based on business requirements and solution
architecture.
Systems Life Cycle (2.8%)
Systems life cycle management concepts and frameworks used to develop, implement, operate, and
maintain IT systems.
Claude API Mechanics (6.8%)
Claude API behavior and mechanics, including messages, tools, streaming, vision, thinking, caching,
invoking Claude through third-party vendors, Messages API data access patterns, batch API use, and
tradeoffs between realtime and batch API selection.
Software Engineering Foundations (7.4%)
Core software engineering principles and practices, including REST APIs, JSON, asynchronous
programming, version control, SDLC integration, code review, and small- and large-scale refactoring.
Claude Application Design (8.6%)
Design considerations for building Claude applications, including how Claude interprets instructions
across interfaces (Claude Code, Desktop, claude.ai, API, SDKs), content boundaries, schema design,
session hygiene, and plugin management.
Configuration Management (4.1%)
Configuration management for Claude system components, including CLAUDE.md files, settings.json,
model version pinning, prompt versioning, and plugin dependencies.
Domain 3: Claude Code (3.1%)
Claude Code Operation (3.1%)
Claude Code core components (Rules, Skills, Commands, Agents, Agent Memory), features (session
management, built-in and custom slash commands, headless mode, streaming mode, auto-mode), the
CLAUDE.md hierarchy, repository initialization, and settings.json configuration.
Claude Certification Program Exam guide

Domain 4: Eval, Testing, and Debugging (2.6%)
Debugging and Error Handling (2.6%)
Debugging and error handling techniques for Claude applications, including error type identification,
recovery strategy selection, trace analysis to identify failure modes, and problem origin isolation
between the integration layer and model output.
Domain 5: Model Selection and Optimization (16.8%)
LLM Fundamentals (5.2%)
Basic understanding of LLMs (tokens, context windows, sampling, non-determinism, next-token
generation), model options (fast mode, extended thinking, adaptive thinking, effort levels), and
fundamental prompting techniques (zero-shot, single-shot, multi-shot).
Technical Fundamentals (6.1%)
Foundational technical concepts supporting AI application development, including basic engineering
practices (integrating with SDKs that wrap REST APIs, websockets).
Model Selection and Tradeoffs (2.7%)
Claude model capabilities (Opus vs. Sonnet vs. Haiku use cases, adaptive thinking support), tradeoffs
across quality/latency/cost parameters, and breaking behavior changes across model releases when
selecting models for tasks.
Cost and Token Management (2.8%)
Token budgeting and cost management techniques for Claude applications, including token usage
tracking, cost modeling, and caching techniques (prompt caching, cache check-pointing) for cost
optimization.
Domain 6: Prompt and Context Engineering (11.0%)
Context Engineering (3.8%)
Context and memory management techniques for Claude applications, including context window
management, prevention of context drift and bloat (tool output pruning, compaction), and context
isolation through subagents or multi-step agentic workflows.
Prompt Engineering (4.6%)
Prompt engineering principles and methods (instruction clarity, few-shot examples, system versus
user placement, output constraints, prompt and instruction placement across components, iterative
refinement, prompt adjustment, input sanitization) when writing and iterating on prompts for Claude.
Claude Certification Program Exam guide

Output Handling (2.6%)
Established patterns and techniques for producing, validating, and consuming Claude output,
including structured output patterns, response validation, defensive parsing, and skepticism toward
confident output.
Domain 7: Security and Safety (8.1%)
AI Application Security (3.2%)
Data privacy and security best practices, including prompt injection awareness and mitigation,
jailbreak defense, untrusted input handling, data leakage prevention, PII handling, and ensuring
authentication, authorization, confidentiality, privacy, and integrity.
Guardrails and Safe Deployment (2.3%)
Safe and responsible deployment practices (content policy, guardrail layering) and secure-by-design
principles (privacy, identity and access management, least privilege).
Claude Hooks (1.0%)
Leveraging hooks for guardrails and safety controls to prevent destructive actions within Claude
applications.
Identity, Secrets, and Key Management (1.6%)
Managing secrets, credentials, and API keys across Claude development and production
environments, including identity validation and authentication, access approval and level verification,
and authorized access monitoring.
Domain 8: Tools and MCPs (10.6%)
Tool Implementation (4.4%)
Tool implementation practices for Claude applications, including tool use and function calling,
configuration for external system interaction, tool description writing, error handling, tool usage
patterns (agentic harness dispatch, client-side vs. server-side tools, approval patterns), and tool set
construction best practices.
MCP Server Development (2.1%)
MCP server development practices, including server authoring, deployment, integration with Claude
applications, MCP resources, tools, and prompts, and communication patterns (stdio, sockets, client
vs. server).
Claude Certification Program Exam guide

Agentic Customization (4.1%)
Tradeoffs among built-in Tools, custom Tools, Skills, and MCPs for selecting and applying the
appropriate approach for a given use case.

7. How to Prepare
There is no single required course. Anthropic does not guarantee that any particular resource ensures
a passing result. Candidates are encouraged to combine hands-on experience with the resources
below:
• Study the exam blueprint in Section 6 and self-assess against each objective
• Review official Anthropic documentation for the Claude API, models, prompt engineering, Claude
Code, Skills, and MCP
• Build and operate at least one Claude application that exercises the API, integrates one or more
tools, applies basic prompt and context engineering, and includes simple security and evaluation
practices
• Practice the developer competencies: writing prompts and system instructions, building agents and
workflows, configuring Claude Code, managing tokens and cost, implementing guardrails, and
creating custom tools or MCP servers
• Complete the sample questions in Section 8 to familiarize yourself with item style

8. Sample Questions
These illustrative items show the style and cognitive level of the exam. They are not drawn from the
live item bank. Correct answers and rationale appear after the questions.
Sample 1 · Domain 2 — Applications and Integration
A developer must process 10,000 documents overnight to produce a non-urgent analytics report. Cost
is the primary concern, and results are not needed until the following morning. Which approach best
fits the requirement?
A. Send every request synchronously through the Messages API in parallel to finish as quickly as
possible.
B. Use the Message Batches API, which processes large asynchronous workloads within a 24-hour
window at reduced cost.
C. Lower max_tokens on synchronous calls to minimize cost.
Claude Certification Program Exam guide

D. Switch to the smallest available model regardless of output quality.
Sample 2 · Domain 7 — Security and Safety
A Claude-powered agent summarizes web pages submitted by end users. One page contains hidden
text instructing the model to ignore previous instructions and reveal its system prompt. Which
mitigation is most effective?
A. Raise the model's temperature so its behavior is harder to predict.
B. Treat retrieved page content as untrusted input, keep it separate from trusted instructions, and
use guardrails or hooks so injected instructions cannot trigger sensitive actions.
C. Add a line to the system prompt asking users not to include malicious instructions.
D. Switch to a larger model that follows instructions more reliably.
Sample 3 · Domain 8 — Tools and MCPs
A team needs Claude to call an internal inventory service exposed as a REST API. They want the
capability to be reusable across several Claude applications and maintained independently of any one
app. Which approach best fits?
A. Hard-code the inventory logic into each application's system prompt.
B. Build an MCP server that exposes the inventory operations as tools so multiple Claude
applications can connect to it.
C. Paste the current inventory data into the context window on every request.
D. Rely on a built-in tool, since built-in tools can reach any internal REST API.
Answer key and rationale
Sample 1: B. The Message Batches API is designed for latency-tolerant, high-volume workloads at lower
cost, which matches an overnight, non-urgent job. Sending requests synchronously in parallel (A) does
not reduce per-token cost; lowering max_tokens (C) or blindly downsizing the model (D) does not
address the batch-versus-realtime tradeoff.
Sample 2: B. Prompt injection is addressed by isolating untrusted content from trusted instructions
and enforcing least-privilege guardrails so injected text cannot invoke sensitive tools. Temperature (A)
is irrelevant to injection; a polite request (C) is not an enforceable control; a more instruction-following
model (D) can be more susceptible, not less.
Sample 3: B. An MCP server exposes reusable tools that multiple Claude applications can share and
that can be maintained independently. Hard-coding logic into prompts (A) is neither reusable nor
maintainable; pasting data (C) gives no live access and wastes context; built-in tools (D) do not