# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a study materials repository for the **Claude Certified Architect (CCA) - Foundations** certification exam by Anthropic. It contains no application code — only documentation and a Claude Code skill.

## Structure

- `doc/claude_architect_study_guide_ptbr.md` — Portuguese translation of the official CCA study guide (covers all 5 domains, visual diagrams, exam tips)
- `doc/skill-cca/` — A Claude Code skill (`architect-guide`) that acts as an interactive exam coach
  - `SKILL.md` — Skill definition with 6 modes: Quiz, Exam Simulation, Domain Deep Dive, Concept Explainer, Browse, ASCII Visualizer
  - `guide.md` — English source-of-truth exam guide (5 domains, 30 task statements, 6 scenarios)
  - `README.md` — Installation and usage instructions

## Key Context

- The skill is designed to be installed at `.claude/skills/architect-guide/` and invoked via `/architect-guide`
- All quiz/exam content must be grounded in `guide.md` — never invented
- The exam has 5 domains weighted: Agentic Architecture (27%), Tool Design & MCP (18%), Claude Code Config (20%), Prompt Engineering (20%), Context Management (15%)
- Pass threshold: 720 / 1000
- Content is bilingual: study guide in Portuguese, skill and exam guide in English
