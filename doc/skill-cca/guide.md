# Claude Certified Architect - Foundations Certification Exam Guide

Source: Anthropic, PBC (Confidential Need to Know)

## Overview

- **Certification**: Claude Certified Architect - Foundations
- **Format**: Multiple choice (1 correct, 3 distractors)
- **Scoring**: 100-1,000 scale, minimum passing score: 720
- **Target**: Solution architects with 6+ months building with Claude APIs, Agent SDK, Claude Code, and MCP

## 5 Content Domains & Weightings

| Domain | Weight | Task Statements |
|--------|--------|-----------------|
| 1. Agentic Architecture & Orchestration | 27% | 7 |
| 2. Tool Design & MCP Integration | 18% | 5 |
| 3. Claude Code Configuration & Workflows | 20% | 6 |
| 4. Prompt Engineering & Structured Output | 20% | 6 |
| 5. Context Management & Reliability | 15% | 6 |

## 6 Exam Scenarios (4 picked at random)

1. **Customer Support Resolution Agent** - Agent SDK, MCP tools (get_customer, lookup_order, process_refund, escalate_to_human), 80%+ first-contact resolution
2. **Code Generation with Claude Code** - Custom slash commands, CLAUDE.md, plan mode vs direct execution
3. **Multi-Agent Research System** - Coordinator-subagent pattern, web search + doc analysis + synthesis + report
4. **Developer Productivity with Claude** - Agent SDK, built-in tools (Read, Write, Bash, Grep, Glob), MCP servers
5. **Claude Code for CI/CD** - Automated code reviews, test generation, PR feedback, minimize false positives
6. **Structured Data Extraction** - JSON schemas, unstructured docs, edge cases, downstream integration

---

## Domain 1: Agentic Architecture & Orchestration (27%)

### 1.1 Design and implement agentic loops for autonomous task execution
- Agentic loop lifecycle: send request -> inspect stop_reason (tool_use vs end_turn) -> execute tools -> return results
- Tool results appended to conversation history for next reasoning step
- Model-driven decision-making (Claude chooses tools) vs pre-configured decision trees
- **Anti-patterns**: Parsing natural language for termination, arbitrary iteration caps, checking assistant text for completion

### 1.2 Orchestrate multi-agent systems with coordinator-subagent patterns
- Hub-and-spoke architecture: coordinator manages all inter-subagent communication
- Subagents have isolated context (don't inherit coordinator's history)
- Coordinator role: task decomposition, delegation, result aggregation, routing by complexity
- Risk: Overly narrow task decomposition -> incomplete coverage
- **Skills**: Dynamic subagent selection, iterative refinement loops, routing all communication through coordinator

### 1.3 Configure subagent invocation, context passing, and spawning
- `Agent` tool (formerly `Task`, renamed v2.1.63) for spawning subagents; `allowedTools` must include "Agent" for coordinator
- Subagent context must be explicitly provided (no automatic inheritance)
- `AgentDefinition` config: descriptions, system prompts, tool restrictions
- `fork_session` for exploring divergent approaches from shared baseline
- **Skills**: Include complete prior findings in subagent prompts, structured data formats for metadata, spawn parallel subagents in single coordinator response

### 1.4 Implement multi-step workflows with enforcement and handoff patterns
- Programmatic enforcement (hooks, prerequisite gates) vs prompt-based guidance
- Deterministic compliance required for financial operations -> use hooks, not prompts
- Structured handoff protocols: customer details, root cause, recommended actions
- **Skills**: Programmatic prerequisites blocking downstream calls, decomposing multi-concern requests, structured escalation summaries

### 1.5 Apply Agent SDK hooks for tool call interception and data normalization
- `PostToolUse` hooks: intercept tool results for transformation before model processes them
- Hooks to enforce compliance rules (blocking refunds above threshold)
- Hooks = deterministic guarantees vs prompts = probabilistic compliance
- **Skills**: PostToolUse for data normalization (timestamps, ISO 8601, status codes), tool call interception to block policy violations

### 1.6 Design task decomposition strategies for complex workflows
- Fixed sequential pipelines (prompt chaining) vs dynamic adaptive decomposition
- Prompt chaining: analyze each file individually, then cross-file integration pass
- Adaptive investigation plans: generate subtasks based on discoveries
- **Skills**: Prompt chaining for predictable reviews, dynamic decomposition for open-ended tasks, splitting large code reviews into per-file + cross-file passes

### 1.7 Manage session state, resumption, and forking
- `--resume <session-name>` to continue named sessions
- `fork_session` for parallel exploration branches
- New session with structured summary > resuming with stale tool results
- **Skills**: Named sessions for investigation work, forking for comparing approaches, informing resumed sessions about file changes

---

## Domain 2: Tool Design & MCP Integration (18%)

### 2.1 Design effective tool interfaces with clear descriptions and boundaries
- Tool descriptions = primary mechanism for LLM tool selection
- Include input formats, example queries, edge cases, boundaries in descriptions
- Ambiguous/overlapping descriptions cause misrouting
- System prompt wording can create unintended tool associations
- **Skills**: Differentiate tools clearly, rename to eliminate overlap, split generic tools into purpose-specific ones

### 2.2 Implement structured error responses for MCP tools
- `isError` flag pattern for communicating failures
- Error categories: transient (timeouts), validation (bad input), business (policy), permission
- Uniform errors prevent appropriate recovery
- Return structured metadata: `errorCategory`, `isRetryable`, human-readable descriptions
- **Skills**: Distinguish access failures from valid empty results, local error recovery in subagents

### 2.3 Distribute tools appropriately across agents and configure tool choice
- Too many tools (18 vs 4-5) degrades selection reliability
- Agents with out-of-role tools tend to misuse them
- Scoped tool access: role-relevant tools only
- `tool_choice` options: "auto", "any", forced selection (`{"type": "tool", "name": "..."}`)
- **Skills**: Restrict subagent tool sets, replace generic with constrained alternatives, forced tool selection for ordering

### 2.4 Integrate MCP servers into Claude Code and agent workflows
- Project-level `.mcp.json` vs user-level `~/.claude.json`
- Environment variable expansion for credentials (`${GITHUB_TOKEN}`)
- MCP resources for content catalogs (issue summaries, database schemas)
- **Skills**: Shared MCP in `.mcp.json`, personal in `~/.claude.json`, community servers for standard integrations

### 2.5 Select and apply built-in tools (Read, Write, Edit, Bash, Grep, Glob) effectively
- Grep = content search, Glob = file pattern matching
- Read/Write for full files, Edit for targeted modifications
- Edit fails on non-unique matches -> Read + Write fallback
- **Skills**: Incremental understanding (Grep -> Read -> follow imports), trace function usage across modules

---

## Domain 3: Claude Code Configuration & Workflows (20%)

### 3.1 Configure CLAUDE.md files with hierarchy, scoping, and modular organization
- Hierarchy: user-level (`~/.claude/CLAUDE.md`), project-level (`.claude/CLAUDE.md` or root), directory-level
- `@import` syntax for modular references
- `.claude/rules/` for topic-specific rule files
- **Skills**: Diagnose hierarchy issues, split large CLAUDE.md into `.claude/rules/`, use `/memory` command

### 3.2 Create and configure custom slash commands and skills
- Project commands in `.claude/commands/` (version controlled)
- User commands in `~/.claude/commands/` (personal)
- Skills in `.claude/skills/` with SKILL.md frontmatter: `context: fork`, `allowed-tools`, `argument-hint`
- **Skills**: `context: fork` for isolation, `allowed-tools` to restrict tool access, `argument-hint` for prompting parameters

### 3.3 Apply path-specific rules for conditional convention loading
- `.claude/rules/` files with YAML frontmatter `paths` fields
- Path-scoped rules load only when editing matching files
- Glob patterns > directory-level CLAUDE.md for cross-directory conventions
- **Skills**: YAML frontmatter path scoping (`paths: ["terraform/**/*"]`), glob patterns for file types (`**/*.test.tsx`)

### 3.4 Determine when to use plan mode vs direct execution
- Plan mode: complex tasks, large-scale changes, multiple valid approaches, architectural decisions
- Direct execution: simple, well-scoped changes
- Explore subagent for verbose discovery without filling main context
- **Skills**: Plan mode for 45+ file migrations, direct for single-file fixes, combine plan + direct execution

### 3.5 Apply iterative refinement techniques for progressive improvement
- Concrete input/output examples > prose descriptions
- Test-driven iteration: write tests first, iterate by sharing failures
- Interview pattern: Claude asks questions before implementing
- **Skills**: 2-3 concrete examples to clarify requirements, test suites before implementation, interview pattern for unfamiliar domains

### 3.6 Integrate Claude Code into CI/CD pipelines
- `-p` flag for non-interactive mode
- `--output-format json` + `--json-schema` for structured output
- CLAUDE.md provides context to CI-invoked Claude Code
- Session isolation: separate review instance from generation instance
- **Skills**: `-p` flag, `--output-format json` for automated posting, prior review findings to avoid duplicates, testing standards in CLAUDE.md

---

## Domain 4: Prompt Engineering & Structured Output (20%)

### 4.1 Design prompts with explicit criteria to reduce false positives
- Explicit criteria > vague instructions ("flag when behavior contradicts code" vs "check comments")
- General "be conservative" instructions fail vs specific categorical criteria
- False positives in one category undermine trust in all categories
- **Skills**: Specific review criteria, temporarily disable high-false-positive categories, explicit severity with code examples

### 4.2 Apply few-shot prompting to improve output consistency
- Few-shot = most effective technique for consistent output when detailed instructions fail
- Demonstrates ambiguous-case handling, judgment generalization
- Reduces hallucination in extraction tasks
- **Skills**: 2-4 targeted examples for ambiguous scenarios with reasoning, examples showing desired format, distinguishing acceptable patterns from issues

### 4.3 Enforce structured output using tool use and JSON schemas
- `tool_use` with JSON schemas = most reliable structured output (eliminates syntax errors)
- `tool_choice`: "auto" (may return text), "any" (must call a tool), forced (specific tool)
- Schemas eliminate syntax errors but NOT semantic errors
- **Skills**: Define extraction tools with JSON schema parameters, `tool_choice: "any"` for guaranteed structure, optional/nullable fields for missing data

### 4.4 Implement validation, retry, and feedback loops for extraction quality
- Retry-with-error-feedback: append specific validation errors to guide correction
- Retries ineffective when info is absent from source (vs format errors)
- `detected_pattern` field for systematic false-positive analysis
- **Skills**: Follow-up with original doc + validation errors, identify when retries won't help, self-correction validation flows

### 4.5 Design efficient batch processing strategies
- Message Batches API: 50% cost savings, up to 24-hour window, no latency SLA
- Batch = non-blocking workloads only (not pre-merge checks)
- Batch API doesn't support multi-turn tool calling
- `custom_id` for correlating request/response pairs
- **Skills**: Match API to latency needs, calculate batch submission frequency, resubmit only failed documents

### 4.6 Design multi-instance and multi-pass review architectures
- Self-review limitation: model retains reasoning context, less likely to question own decisions
- Independent review instances > self-review or extended thinking
- Multi-pass: per-file local analysis + cross-file integration passes
- **Skills**: Second independent Claude instance for review, per-file + integration passes, verification passes with confidence scores

---

## Domain 5: Context Management & Reliability (15%)

### 5.1 Manage conversation context to preserve critical information
- Progressive summarization risks: numerical values, dates, expectations condensed away
- "Lost in the middle" effect: models process beginning and end reliably, may omit middle
- Tool results accumulate tokens disproportionately to relevance
- **Skills**: Extract transactional facts into persistent "case facts" block, trim verbose tool outputs, summaries at beginning + section headers

### 5.2 Design effective escalation and ambiguity resolution patterns
- Escalation triggers: customer requests human, policy gaps, inability to progress
- Sentiment-based escalation and confidence scores are unreliable proxies
- Multiple customer matches -> request additional identifiers (not heuristic selection)
- **Skills**: Few-shot escalation criteria, honor explicit human requests immediately, escalate on policy ambiguity

### 5.3 Implement error propagation strategies across multi-agent systems
- Structured error context: failure type, attempted query, partial results, alternatives
- Access failures (timeouts) vs valid empty results (no matches)
- Generic error statuses hide context; silent suppression and total termination are anti-patterns
- **Skills**: Structured error context, distinguish failures from empty results, local recovery in subagents, coverage annotations

### 5.4 Manage context effectively in large codebase exploration
- Context degradation: models give inconsistent answers, reference "typical patterns" instead of specifics
- Scratchpad files for persisting key findings across context boundaries
- Subagent delegation for verbose exploration
- Structured state persistence for crash recovery (manifests)
- **Skills**: Subagents for investigation, scratchpad files, summarize between phases, `/compact` for context reduction

### 5.5 Design human review workflows and confidence calibration
- Aggregate accuracy (97%) may mask poor performance on specific types/fields
- Stratified random sampling for error rate measurement
- Field-level confidence scores calibrated with labeled validation sets
- **Skills**: Stratified sampling, accuracy by document type and field, confidence calibration, route low-confidence to human review

### 5.6 Preserve information provenance and handle uncertainty in multi-source synthesis
- Source attribution lost during summarization without claim-source mappings
- Structured claim-source mappings required for synthesis
- Conflicting statistics: annotate conflicts with source attribution (don't arbitrarily pick one)
- Temporal data: include publication/collection dates to prevent false contradictions
- **Skills**: Subagents output claim-source mappings, annotate conflicts, include dates, render content types appropriately

---

## Sample Questions Summary

| Q# | Scenario | Correct | Key Concept |
|----|----------|---------|-------------|
| 1 | Customer Support | A | Programmatic prerequisites > prompt-based enforcement |
| 2 | Customer Support | B | Tool descriptions are primary mechanism for selection |
| 3 | Customer Support | A | Few-shot escalation criteria > confidence scores |
| 4 | Code Gen / Claude Code | A | `.claude/commands/` for shared team commands |
| 5 | Code Gen / Claude Code | A | Plan mode for large-scale restructuring |
| 6 | Code Gen / Claude Code | A | `.claude/rules/` with glob patterns for path-specific conventions |
| 7 | Multi-Agent Research | B | Narrow coordinator decomposition = root cause of coverage gaps |
| 8 | Multi-Agent Research | A | Structured error context for intelligent recovery |
| 9 | Multi-Agent Research | A | Scoped cross-role tools (verify_fact) for high-frequency needs |

## Preparation Strategy

- Hands-on experience is essential alongside this guide
- Practice with all 6 scenarios
- Focus on Domain 1 (27% weight) and understanding tradeoffs
- Key recurring themes: programmatic enforcement > prompt guidance, structured errors, scoped tools, independent review instances
