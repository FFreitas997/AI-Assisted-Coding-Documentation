# AI-Assisted Coding Documentation

Practical notes for learning **AI fundamentals** and using tools such as **GitHub Copilot, CodeRabbit, Claude Code, Gemini CLI, OpenClaw, and MCP servers** in real software projects.

This repository is intentionally documentation-first: the goal is to explain concepts clearly and show **small examples** you can copy into your own workflow.

## Table of Contents

1. [AI Fundamentals](#ai-fundamentals)
2. [GitHub Copilot Overview](#github-copilot-overview)
3. [Automated Code Review with CodeRabbit](#automated-code-review-with-coderabbit)
4. [CLI-Based AI Agents](#cli-based-ai-agents)
5. [OpenClaw](#openclaw)
6. [Advanced Workflows and MCP](#advanced-workflows-and-mcp)
7. [AI Code Quality Checklist](#ai-code-quality-checklist)
8. [When to Use What](#when-to-use-what)

## AI Fundamentals

### Tokens

Tokens are the small chunks an AI model reads and writes. A token is not exactly a word:

- `"documentation"` may be split into multiple tokens
- punctuation also counts as tokens
- code uses many tokens quickly because identifiers, braces, and strings all count

**Why it matters:** more tokens = more cost, more latency, and less space left in the context window.

**Small example**

```text
Short prompt:
"Explain this function."

Better prompt:
"Explain this function in 3 bullet points. Focus on inputs, outputs, and side effects."
```

The second prompt usually uses tokens more efficiently because it asks for a tighter answer.

### Context Window

The context window is how much information the model can consider at one time:

- your prompt
- attached files
- previous chat messages
- tool results
- the model's own generated output

**Why it matters:** if the conversation gets too large, important details may be dropped or summarized.

**Rule of thumb:** give the model the *right* context, not *all* context.

### Agent

An **agent** is an AI system that does more than answer one prompt. It can:

- plan steps
- inspect files
- run tools
- edit code
- retry after failures

**Simple difference**

- **Chat response:** "Here is how you could fix it."
- **Agent behavior:** "I found the bug, updated the file, ran the test, and fixed the failing assertion."

### Hallucinations

Hallucinations happen when the model confidently says something that is wrong or unsupported.

Common examples:

- inventing an API that does not exist
- claiming a test passed when it was never run
- describing a library feature from an older version

**How to reduce hallucinations**

- ask for citations or file references
- provide exact files or docs
- ask it to say "I don't know" when unsure
- verify generated code before accepting it

### Prompts

A simple formula is:

> **What + How + Return type**

**Example**

```text
What: Summarize this pull request
How: Focus on risks, breaking changes, and rollout notes
Return type: 5 bullet points
```

**Another example for coding**

```text
Update this README section.
Use simple English and one code example.
Return markdown only.
```

### Use AI For / Code Yourself

Use AI heavily for:

- first drafts
- summarizing unfamiliar code
- writing tests from existing behavior
- boilerplate
- refactoring suggestions
- documentation

Code it yourself, or review very carefully, when dealing with:

- security-sensitive code
- authentication and permissions
- money, billing, and legal logic
- production migrations
- critical algorithms
- anything you do not understand well enough to explain

### MCP Servers

**MCP** stands for **Model Context Protocol**. It is a standard way for AI tools to connect to external tools and data sources.

Without MCP, the model only knows what you paste into the chat.  
With MCP, the model can often use tools directly.

**Examples of MCP servers**

- **Filesystem**: read and edit repository files
- **Browser / Puppeteer**: test websites automatically
- **DuckDuckGo or web search**: look up current docs
- **Postgres**: run database queries
- **Memory**: store project-specific facts for later sessions

**Small example**

```text
Without MCP:
"Here is a stack trace. Can you guess the issue?"

With MCP:
"Open the logs, inspect the config file, run the failing query, and summarize the root cause."
```

## GitHub Copilot Overview

GitHub Copilot helps developers inside their editor with **code completion, chat, planning, and agent-style task execution**.

### How it works in different IDEs

| Tool | Typical experience |
| --- | --- |
| **VS Code** | Strongest overall Copilot experience: inline completions, chat, Ask/Plan/Agent modes, slash commands, and chat participants |
| **Visual Studio** | Good for .NET and solution-based workflows: chat, explanations, test help, and in-editor suggestions |
| **JetBrains IDEs** | Good fit for IntelliJ, PyCharm, WebStorm, and friends: inline suggestions plus project-aware chat |

### Customize Copilot for a project

For project-wide guidance, create repository instructions.

- If your IDE supports **`/init`**, use it to scaffold starter instructions.
- You can also create them manually in `.github/copilot-instructions.md`.

**Real example**

```md
# .github/copilot-instructions.md

- This repository is documentation-first.
- Prefer clear markdown headings and short paragraphs.
- Add one small example for each major concept.
- Do not invent commands; when unsure, say that the command should be verified in current docs.
- Keep examples practical and beginner-friendly.
```

**What `/init` is useful for**

- creating a starting instruction file quickly
- capturing coding standards
- reminding Copilot how to build, test, or validate changes
- defining repository-specific tone and conventions

### Modes: Ask, Plan, Agent

| Mode | Best for |
| --- | --- |
| **Ask** | Questions, explanations, quick suggestions |
| **Plan** | Breaking a task into steps before changing files |
| **Agent** | Letting Copilot inspect files, edit code, run commands, and iterate |

**Small example**

```text
Ask: "Explain why this SQL query is slow."
Plan: "Create a step-by-step plan to migrate this endpoint safely."
Agent: "Rename this API field across the backend, tests, and docs."
```

### Chat participants

Common participants include:

- **`@workspace`**: use project context
- **`@terminal`**: reason about terminal output and commands
- **`@vscode`**: ask about editor features and settings

**Small examples**

```text
@workspace Where is the authentication middleware defined?
@terminal Why did this npm script fail?
@vscode How do I enable format-on-save for this project?
```

### Useful slash commands

Common commands include:

- **`/doc`**: generate or improve documentation
- **`/explain`**: explain code or output
- **`/fix`**: suggest a fix
- **`/tests`**: generate or improve tests
- **`/new`**: scaffold a new file or component

**Small example**

```text
/tests Create focused unit tests for this parser.
```

## Automated Code Review with CodeRabbit

CodeRabbit focuses on **pull request review** rather than real-time coding.

### What it is useful for

- automatic PR review
- security analysis
- code quality suggestions
- natural-language discussion on a PR
- team learning through repeated feedback
- support across platforms such as GitHub, GitLab, and Azure DevOps

### How it works

Typical flow:

1. Developer opens or updates a pull request
2. CodeRabbit analyzes the diff
3. It posts a summary and line-level comments
4. Developer replies, pushes fixes, or asks follow-up questions
5. CodeRabbit re-reviews the updated PR

### CodeRabbit commands and interactions

CodeRabbit is usually **PR-driven and automatic**, but you will often see prompts and placeholders such as:

- `@coderabbitai summary` in a PR description placeholder
- `@coderabbitai title` in a PR title placeholder
- replies in the PR conversation to clarify or request another look

**Important:** exact chat commands and CLI flags can evolve, so verify the latest syntax in the official docs before teaching a team to standardize on them.

### Configure CodeRabbit with YAML

A common setup is a `.coderabbit.yaml` file in the project root.

**Small example**

```yaml
language: en-US
reviews:
  profile: chill
  high_level_summary: true
  review_status: true
  collapse_walkthrough: false
  auto_review:
    enabled: true
    drafts: false
chat:
  auto_reply: true
```

This kind of config controls review style, auto-review behavior, and chat behavior.

### CodeRabbit CLI

The CLI is useful when you want **review-style feedback before or outside a PR**.

Typical workflow:

1. authenticate
2. point the CLI at a diff, branch, or PR
3. read the review output
4. fix issues locally before pushing

Use the CLI when:

- you want feedback before opening a PR
- you want to automate local review in CI or scripts
- you prefer a terminal-first workflow

### CodeRabbit plan workflow

A practical way to use it:

1. **Describe your idea**
2. **Generate a plan**
3. **Collaborate on the PR**
4. **Go hands-off for repeated review cycles**

### CodeRabbit review PR example

```text
1. Open PR
2. Read CodeRabbit summary first
3. Fix high-confidence issues
4. Reply on unclear comments
5. Push updates
6. Re-check the new review state before merge
```

## CLI-Based AI Agents

CLI agents are good when you want a tool that can stay in your terminal session, inspect files, and work across many steps.

### Claude Code

Claude Code is a terminal-based coding agent that understands your repository and can help with complex reasoning and multi-step work.

Why people like it:

- strong reasoning for complex tasks
- natural terminal workflow
- can stay on one task for a long session
- works well for repo-wide refactors, debugging, and architecture discussion

#### Practical "thinking mode" escalation

Teams often escalate prompting depth like this:

1. `think`
2. `think hard`
3. `think harder`
4. `ultrathink`

Use the lighter prompts for small tasks and the heavier prompts for:

- deep debugging
- architecture decisions
- risky refactors
- planning across many files

#### CLAUDE.md

`CLAUDE.md` is a project context file used to guide Claude Code in a repository.

**Small example**

```md
# CLAUDE.md

- Use TypeScript strict mode.
- Run unit tests before proposing completion.
- Prefer small PRs.
- Explain tradeoffs before making architecture changes.
```

### Gemini CLI

Gemini CLI is an open-source AI agent for the terminal.

Highlights:

- **1M token context window**
- **generous free tier** for many individual workflows
- strong fit for large codebases and large documentation sets
- built-in tools plus MCP support
- custom context through `GEMINI.md`

**Small examples**

```bash
gemini
```

```bash
gemini -p "Explain the architecture of this repository" --output-format json
```

Gemini CLI is especially useful when you want to feed a lot of context into one session without constantly trimming files.

## OpenClaw

OpenClaw is a self-hostable personal AI assistant platform.

### What it offers

- chat-style interface
- persistent sessions that act like long-lived working memory
- extensible skills
- tool access such as browser, shell, files, and automation features
- multi-agent workflows that can spawn dedicated agents or sessions
- self-hosted control over your environment

### Useful concepts

- **Skills**: reusable capabilities you can install or share
- **Sessions**: ongoing conversations and task history
- **Cron jobs**: scheduled automations
- **Workspace**: the environment where the agent and skills operate

**Small examples**

```text
"Every morning at 9 AM, summarize unread support emails."
```

```text
"Open the release notes, compare them to yesterday's build, and send a Slack summary."
```

OpenClaw is a good fit when you want **background automation** rather than just a one-shot coding assistant.

## Advanced Workflows and MCP

### Combining tools

One practical workflow looks like this:

1. **Planning**: use Claude Code with `ultrathink` for architecture and design
2. **Implementation**: use GitHub Copilot for fast in-editor coding
3. **Code review**: let CodeRabbit review the PR and catch issues before merge
4. **Documentation**: use Gemini CLI when you need to process a lot of context

This works well because each tool does a different job well.

### Model Context Protocol (MCP)

#### Without MCP

Without MCP, the AI:

- only knows what you tell it
- cannot check live documentation by itself
- cannot use external tools directly
- depends on manual copy/paste of context

#### With MCP

With MCP, the AI can:

- search the web for current information
- connect to databases and APIs
- test websites automatically
- remember project details through memory tools
- inspect and change repository files with tool support

#### Popular MCP server examples

- **Puppeteer / browser automation**
- **DuckDuckGo or search tools**
- **Filesystem / repo management**
- **Postgres**
- **Memory / persistent memory**

## AI Code Quality Checklist

### Common AI mistakes

- changing more files than necessary
- inventing APIs or commands
- writing tests that only validate the happy path
- ignoring edge cases
- breaking style or architecture conventions
- adding code the developer cannot maintain

### Before accepting AI output

Ask yourself:

- Do I understand this change?
- Did the tool cite real files, commands, or docs?
- Were tests actually run?
- Does the solution handle edge cases?
- Is anything security-sensitive involved?
- Is this simpler than doing it manually?

## When to Use What

| Tool | Best use |
| --- | --- |
| **GitHub Copilot** | Real-time coding in VS Code, fast completions, editor chat |
| **CodeRabbit** | PR reviews, security analysis, team consistency |
| **Claude Code** | Complex refactoring, architecture discussions, long autonomous sessions |
| **Gemini CLI** | Large codebases, large docs, terminal workflows, big context needs |
| **OpenClaw** | Background automation, multi-tool orchestration, proactive assistance |

## Final Advice

Do not think of AI tools as one replacement for all work.  
Think of them as a **stack**:

- **Copilot** for speed
- **Claude Code** for deep reasoning
- **Gemini CLI** for large-context exploration
- **CodeRabbit** for review
- **OpenClaw** for automation
- **MCP** for connecting all of them to real tools and real data
