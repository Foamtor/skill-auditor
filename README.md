# skill-auditor

> A linter for SKILL.md files. Scores instruction-following quality across 10 dimensions, auto-detects skill type, and suggests improvements.

[English](README.md) | [中文](README_CN.md)

## What is this?

**skill-auditor is a linter for AI agent skill files.**

Just as ESLint checks JavaScript code quality and Pylint checks Python, skill-auditor checks whether a SKILL.md file contains the patterns that make AI agents actually follow the instructions inside it.

It answers one question: **"Does this SKILL.md have the structural elements that make AI comply, or is it just a block of natural language that AI will likely ignore?"**

## The problem

You write a detailed SKILL.md telling the AI to "first fetch the source, then download images, then write, then review." The AI says "got it" and skips straight to step 3.

This happens because LLMs:
- Don't have persistent memory — every session starts from scratch
- Follow instructions probabilistically, not deterministically
- Rationalize skipping steps — "this is simple enough, I don't need to fetch the source"

Community projects like [Superpowers](https://github.com/obra/superpowers) (24k+ stars) have developed techniques to solve this: anti-rationalization guards, stage gates, verification scripts, trap checklists. But **there's no way to know if your SKILL.md actually uses these techniques** — until the AI fails and you notice.

skill-auditor solves this by scanning your SKILL.md for 10 evidence-based compliance patterns and giving you a scored report.

## Who is this for?

- **Skill authors**: Check if your SKILL.md has the patterns that make AI comply before publishing
- **Team leads**: Audit shared skills across a team's agent setup
- **CI pipelines**: Add as a quality gate — reject skills that lack critical patterns
- **AI agent users**: Evaluate third-party skills before installing them

## What it checks (10 dimensions)

| # | Dimension | What's missing if it fails |
|---|-----------|---------------------------|
| 1 | Anti-rationalization guard | AI will find excuses to skip steps |
| 2 | Stage gates | AI will run through the entire workflow without stopping |
| 3 | Automated verification | AI will self-report "passed" without actually checking |
| 4 | Decision flowchart | AI will get lost in vague "then"/"if" instructions |
| 5 | Trap checklist | AI will repeat known mistakes that others already documented |
| 6 | Progressive disclosure | Context window will be overloaded, AI ignores key rules |
| 7 | Three-layer architecture | Skill tries to do everything instead of collaborating with AGENTS.md/MCP |
| 8 | Runtime hooks | Pure text instructions with no code-level enforcement |
| 9 | Context Engineering | Key rules buried in the middle, file too long |
| 10 | Scoped rules | All rules loaded all the time, even when irrelevant |

**Not all 10 dimensions apply to every skill.** A short reference-type skill doesn't need stage gates. skill-auditor auto-detects the skill type and only checks applicable dimensions:

| Skill type | Applicable dims | Example |
|------------|----------------|---------|
| Workflow | 10/10 | Writing pipelines, multi-step processes |
| Tool | 7/10 | Scripts, CLI wrappers |
| Reference | 4/10 | Cheat sheets, API docs |
| Pattern | 3/10 | Methodologies, thinking frameworks |

## What it does NOT do

- ❌ Does not check factual accuracy of skill content
- ❌ Does not fix skills — it reports problems, you fix them
- ❌ Does not guarantee AI will follow the skill (compliance depends on the AI model, context, and task)
- ❌ Does not replace manual review — the script detects patterns objectively; an AI agent should supplement with subjective judgment

## Install

```bash
npx skills add Foamtor/skill-auditor
```

Or clone directly:

```bash
git clone https://github.com/Foamtor/skill-auditor.git ~/.agents/skills/skill-auditor
```

Compatible with: Claude Code, OpenAI Codex, Cursor, Gemini CLI, Hermes Agent, and any tool that supports the [Agent Skills standard](https://agentskills.io).

## Usage

```bash
# Audit a single skill
python3 scripts/audit_skill.py /path/to/SKILL.md

# JSON output (for CI integration)
python3 scripts/audit_skill.py /path/to/SKILL.md --json
```

### Exit codes

| Code | Meaning |
|------|---------|
| 0 | All required dimensions pass |
| 1 | One or more required dimensions fail |
| 2 | Parameter error |

Use in CI to reject skills that lack critical patterns:

```bash
python3 scripts/audit_skill.py my-skill/SKILL.md || echo "Skill quality check failed"
```

## License

MIT
