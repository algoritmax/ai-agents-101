![License](https://img.shields.io/github/license/algoritmax/ai-agents-101)
![Stars](https://img.shields.io/github/stars/algoritmax/ai-agents-101)

# AI Agents 101

A collection of fundamental concepts, resources, and references for understanding AI agents.

## 🧩 Fundamentals

Core concepts you need to understand before diving into AI agents.

### What is an AI Agent?
Autonomous systems that perceive, reason, and act to achieve goals. Unlike simple chatbots that react to prompts, agents maintain state, use tools, and work toward objectives across multiple interactions.

> "An agent is anything that can perceive its environment and act upon that environment." — Chip Huyen

### Workflows vs Agents
Anthropic draws an important architectural distinction between these two patterns:

- **Workflows** - Systems where LLMs and tools are orchestrated through predefined code paths
- **Agents** - Systems where LLMs dynamically direct their own processes and tool usage, maintaining control over how they accomplish tasks

Use agents for open-ended problems where it's difficult to predict the required number of steps, and where you can't hardcode a fixed path.

### The Agent Loop
The fundamental cycle that powers all agents: observe, think, act, repeat. Each iteration gathers new information, reasons about the current state, and takes action to move closer to the goal.

### Context Engineering
The practice of curating and managing the information flow at every turn of an agentic system. This involves deciding what information the model sees, when it sees it, and how it's structured.

- **Global Context** - Information accessed from the external world
- **AGI Maximalism vs Context Engineering** - Rather than waiting for a god-like model, we must actively engineer the environment (memory, tools, offloading) around current models to extract tangible value

### Human-in-the-Loop
Humans can be involved at any stage to aid with the process and mitigate risks. This is essential for high-stakes decisions and helps catch errors before they compound.

---

## 🛠️ Core Components

The building blocks that make up an AI agent.

### Augmented LLM
The combination of retrieval, tools, and memory that transforms a base LLM into something more capable. This is the foundation upon which agents are built.

### LLM as the Brain
Large language models power agent reasoning, serving as the cognitive engine that interprets observations, makes decisions, and generates actions.

### Memory Systems
Short-term, long-term, and retrieval-augmented memory that allows agents to persist and recall information across interactions.

- **Offloading** - Moves data to original storage
- **Compaction** - Summarizes history
- **KV Cache** - Caches the processed state of past tokens, exploiting invariant history

### Tool Use
Enabling agents to interact with external systems. Successful agents have very few tools—complexity comes from composition, not quantity.

- **Computer as a Primitive** - Giving an agent direct access to shell and file system rather than just a desktop interface
- **Tool Offloading** - Moving tool definitions to file system to solve context bloat from large MCP servers
- **Optional Parameters** - Modify tool schemas to treat parameters as optional, preventing hallucination when agent lacks information

### Planning & Reasoning
Planning, at its core, is a search problem. Chain-of-thought, ReAct, and other reasoning patterns help agents break down complex tasks and think step-by-step.

- **Recitation Technique** - Agent offloads its plan to a file and deliberately pulls it back into context during execution
- **Intent Classification** - Often used to help agents route to appropriate actions; some intents should be classified as irrelevant
- **Decouple Planning from Execution** - Generate plans, validate them, then execute separately

**Tips for better planning:**
- Write better system prompts with more examples
- Give better descriptions of tools and their parameters
- Refactor complex functions into simpler ones
- Use stronger models (generally better at planning)
- Fine-tune a model specifically for plan generation

**Reflection methods:**
- [ReAct](https://arxiv.org/abs/2210.03629) - Synergizing reasoning and acting
- [Reflexion](https://arxiv.org/abs/2303.11366) - Verbal reinforcement learning

### Sandboxing
Provides isolated environment, shell, and file system for safe agent execution without risking the host system.

---

## 🏗️ Agent Architectures

Common patterns and frameworks for building agents.

### Single Agent
One LLM handling all tasks end-to-end. Simple to build and debug, but can struggle with complex multi-domain problems.

### Multi-Agent Systems
Multiple specialized agents collaborating to solve problems that exceed any single agent's capabilities.

- **Context Isolation** - Using sub-agents for tasks that produce high volume of intermediate noise ("data exhaust")
- **Ralph Wiggum Loop Coordination** - Persistent files like progress.txt, task lists, and git history
- **Multiplayer Mode Problem** - Difficulty of multiple agents/humans collaborating on same data pool without conflicts

### Hierarchical Agents
Manager agents delegating to worker agents, creating a chain of command that mirrors organizational structures.

- **RLMs** - Learn when to spawn sub-agents automatically, unlike traditional heuristic prompts

### Workflow Patterns
Predefined orchestration patterns that are simpler than full agents:

- **Prompt Chaining** - Sequential LLM calls where each output feeds the next
- **LLM Routing** - Routing calls to different LLMs or classification algorithms
- **Parallelization** - Breaking tasks into independent subtasks (sectioning) or running same task multiple times (voting)
- **Orchestrator-Workers** - Central LLM dynamically breaks down tasks and delegates to workers
- **Evaluator-Optimizer** - One LLM generates responses while another evaluates and provides feedback in a loop

---

## 🧠 Advanced Concepts

### Spec-Driven Development
Designing outcomes so code can be regenerated from specifications. Instead of treating code as the artifact, the specification becomes the source of truth. Agents can regenerate implementations from well-written specs, making the quality of specifications paramount.

> [!TIP]
> Think of AI agents as "stochastic CNC machines"—given a deterministic specification, they produce regenerable outcomes.

### Skill Systems
Provide SOPs (Standard Operating Procedures) that encode expert knowledge and best practices into reusable agent behaviors.

### Skill Learning
Automatically identifying common patterns in trajectories and saving them as new SOPs. Agents improve by extracting successful strategies from their own execution history.

### Continual Learning in Token Space
Evolving an agent's prompts, skills, and memories externally without updating model weights. The model stays frozen while its context adapts.

- **Token-space vs Weight-space Learning** - External memory/prompt evolution vs fine-tuning

### Reflection over Agent Trajectories
Learning from past execution paths by analyzing what worked, what failed, and why. This step significantly boosts agent performance.

### Metacognition
An agent's emerging ability to figure out how to use tools it has not been explicitly trained on—learning to learn in context.

### Population of Prompts
Testing multiple prompt variations and evolving them based on trajectory scores. DSPy implements this approach for systematic prompt optimization.

---

## ⚠️ Challenges & Pain Points

### Compound Mistakes
An agent often needs multiple steps to accomplish a task, and overall accuracy decreases as steps increase. If model accuracy is 95% per step, over 10 steps accuracy drops to 60%, and over 100 steps it's only 0.6%.

### Agent Memory Difficulty
The dual challenge of what to write (save) and what to fetch (retrieve). Getting this wrong leads to either bloated context or missing critical information.

### API Costs
A common complaint is that agents are only good for burning through API credits. However, if agents can be autonomous, they can save human time, making their costs worthwhile.

### File System Limitations
Basic tools like grep and glob fall apart as repo sizes grow, requiring specialized semantic search to remain effective.

### Concurrency
Managing simultaneous agents without conflicting decisions or data corruption is currently unsolved.

- **Multi-writer Conflicts** - File systems lack robust mechanisms for handling concurrency

### Guardrails
Listed as an important challenge in Gartner's 2025 trends report. Ensuring agents stay within acceptable bounds of behavior.

### Security Concerns
MCP (Model Context Protocol) usage introduces specific risks:

- **Command Injection** - Malicious input exploiting tool execution
- **Tool Poisoning** - Malicious instructions hidden inside tool definitions
- **Tool Redefinition** - Overwriting legitimate tools with malicious versions
- **Cross-Server Tool Shadowing** - Tools from one server masquerading as another

### File Systems vs Databases for Agent Memory
Databases were invented in the 1960s-70s specifically because file systems couldn't handle multiple writers/readers, resource contention, scaling, and schemas. File systems are popular now because many agents are designed for coding, where data naturally lives in files.

> [!WARNING]
> Assuming all agent memory should be files is "overfitting to the coding use case."

- **Hybrid Model** - Like humans using local files for drafts but cloud databases for collaboration, agents will likely use file systems for local execution and databases for shared, durable memory
- **Prediction** - Long-term context (skills, organizational knowledge, shared memories) will migrate to databases

---

## 💼 Use Cases

### Coding Agents
Agents are particularly effective for coding because:

- Code solutions are verifiable through automated tests
- Agents can iterate using test results as feedback
- The problem space is well-defined and structured
- Output quality can be measured objectively

### Customer Support
Combines familiar chatbot interfaces with enhanced capabilities through tool integration. A natural fit for open-ended agents. Some companies use usage-based pricing that charges only for successful resolutions.

### Research & Report Generation
Autonomous research agents can gather data, synthesize information, and produce comprehensive reports with citations.

### Browser Automation
Agents that can navigate websites, fill forms, and extract information programmatically.

### Other Applications
- Create websites
- Plan trips
- Market research
- Manage customer accounts
- Automate data entry
- Interview preparation
- Negotiate deals

---

## 🔧 Popular Frameworks

Tools and libraries for building AI agents.

### General Purpose

- **[LangGraph](https://github.com/langchain-ai/langgraph)** - Library for building stateful, multi-actor applications with LLMs
- **[CrewAI](https://www.crewai.com/)** - Popular framework for orchestrating role-playing AI agents
- **[AutoGen](https://github.com/microsoft/autogen)** - Microsoft's framework for multi-agent conversations
- **[OpenAI Agents SDK](https://openai.github.io/openai-agents-python/)** - Official OpenAI multi-agent framework (evolved from Swarm)
- **[PydanticAI](https://ai.pydantic.dev/)** - Agent framework built on Pydantic
- **[smolagents](https://github.com/huggingface/smolagents)** - HuggingFace's barebones library for agents that think in Python code
- **[LlamaIndex](https://github.com/run-llama/llama_index)** - Framework for building LLM-powered agents over your data

### Coding & Development

- **[Claude Code](https://docs.anthropic.com/en/docs/claude-code)** - Anthropic's CLI agent for software development
- **[OpenHands](https://github.com/All-Hands-AI/OpenHands)** - Agents that can modify code, run commands, browse web, and call APIs
- **[Cursor](https://cursor.sh/)** - AI code editor utilizing semantic search
- **[Windsurf](https://codeium.com/windsurf)** - AI-powered IDE by Codeium
- **[Devin](https://devin.ai/)** - Collaborative AI teammate for software development

### Research & Reports

- **[GPT Researcher](https://github.com/assafelovic/gpt-researcher)** - Autonomous agent for comprehensive web and local research
- **[Stanford STORM](https://github.com/stanford-oval/storm)** - LLM-powered system that researches topics and generates full-length reports with citations

### Browser Automation

- **[Stagehand](https://github.com/browserbase/stagehand)** - AI browser automation
- **[LaVague](https://github.com/lavague-ai/LaVague)** - Large Action Model framework for AI web agents

### Data & RAG

- **[RAGFlow](https://github.com/infiniflow/ragflow)** - Open-source RAG engine with deep document understanding
- **[ScrapeGraphAI](https://github.com/ScrapeGraphAI/Scrapegraph-ai)** - LLM-powered web scraping
- **[Firecrawl](https://github.com/mendableai/firecrawl)** - Turn websites into LLM-ready markdown

### Other Tools

- **[DSPy](https://dspy-docs.vercel.app/)** - Framework for optimizing prompts and LM pipelines
- **[Fabric](https://github.com/danielmiessler/fabric)** - Framework for augmenting humans using AI
- **[Tavily](https://tavily.com/)** - Search engine tailored for AI agents
- **[Amazon Bedrock Agents](https://aws.amazon.com/bedrock/agents/)** - AWS managed agent service
- **[Semantic Kernel](https://github.com/microsoft/semantic-kernel)** - Microsoft's SDK for integrating LLMs into apps

---

## 📖 Related Repositories

Links to deeper dives and implementations.

- [AutoGPT](https://github.com/Significant-Gravitas/AutoGPT) - Most starred agent repository
- [BabyAGI](https://github.com/yoheinakajima/babyagi) - Minimalist task-driven autonomous agent
- [CodeAct (Apple)](https://machinelearning.apple.com/research/codeact) - Uses executable Python code to consolidate LLM agents' actions
- [OpenAgents](https://github.com/xlang-ai/OpenAgents) - Open platform for language agents in the wild
- [UI-TARS](https://github.com/bytedance/UI-TARS) - ByteDance's GUI interaction agent
- [cognee](https://github.com/topoteretes/cognee) - Reliable LLM memory for AI applications
- [AI Enablement Stack](https://github.com/daytonaio/ai-enablement-stack) - Community-driven mapping of AI development tools

---

## 📚 Resources

### Recommended Reading

- [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) - Anthropic's comprehensive guide
- [Agents](https://huyenchip.com/2025/01/07/agents.html) - Chip Huyen's deep dive
- [Agentic AI Handbook](https://www.nibzard.com/agentic-handbook) - 113 production patterns for shipping AI agents
- [Claude Diary](https://rlancemartin.github.io/2025/12/01/claude_diary/) - Lance Martin on effective agent design
- [The DNA of AI Agents](https://cedricchee.com/blog/the-dna-of-ai-agents/) - Common patterns in agent design
- [Orchestrating Agents](https://cookbook.openai.com/examples/orchestrating_agents) - OpenAI Cookbook

### Papers

- [ReAct: Synergizing Reasoning and Acting](https://arxiv.org/abs/2210.03629) - Foundational paper on reasoning + acting
- [Reflexion: Verbal Reinforcement Learning](https://arxiv.org/abs/2303.11366) - Learning from verbal feedback
- [Voyager: Open-Ended Embodied Agent](https://arxiv.org/abs/2305.16291) - Minecraft agent with skill library
- [Toolformer: LMs Teaching Themselves Tools](https://arxiv.org/abs/2302.04761) - Self-supervised tool learning
- [SWE-agent: Agent-Computer Interfaces](https://arxiv.org/abs/2405.15793) - Automated software engineering
- [Gorilla: LLM Connected with APIs](https://arxiv.org/abs/2305.15334) - API-calling capabilities

### Videos / Podcasts

- [Context Engineering Episode 3](https://youtu.be/MJScoDgIcXg?si=q5BVpayk_DYAktdP) - Lance Martin / LangChain / Chroma

### People to Follow

- **Lance Martin** - LangChain
- **Chip Huyen** - Author of "Designing Machine Learning Systems"
- **Simon Willison** - Creator of Datasette, prolific AI blogger
- **Daniel Miessler** - Creator of Fabric AI framework
- **Tobi Lutke** - Shopify CEO, early adopter of AI-augmented workflows
- **Sam Altman** - OpenAI

### Industry Reports

- [Gartner Top 10 Strategic Technology Trends 2025](https://www.gartner.com/en/articles/top-technology-trends-2025) - Includes agentic AI
- [Why Agents Are the Next Frontier](https://www.mckinsey.com/capabilities/mckinsey-digital/our-insights/why-agents-are-the-next-frontier-of-generative-ai) - McKinsey analysis
- [Agentic AI's Opportunities and Risks](https://scet.berkeley.edu/the-next-next-big-thing-agentic-ais-opportunities-and-risks/) - UC Berkeley

### Protocols

- [Agent2Agent Protocol (A2A)](https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/) - Google's agent interoperability standard
- [Model Context Protocol](https://www.anthropic.com/news/model-context-protocol) - Anthropic's MCP
