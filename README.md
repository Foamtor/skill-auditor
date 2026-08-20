# skill-auditor

> Evaluate how well a SKILL.md constrains AI behavior. Find weak spots that cause unstable execution, and get actionable improvement suggestions.

[English](README.md) | [中文](README_CN.md)

## What is this?

**skill-auditor evaluates the constraint strength of AI agent skill files.**

When you write a SKILL.md and give it to an AI agent, the execution results are often inconsistent: sometimes the AI follows the workflow precisely, sometimes it skips steps, sometimes it rationalizes doing less work. The root cause is that most SKILL.md files lack the structural constraints that force AI to comply.

skill-auditor scans a SKILL.md file for 10 constraint patterns that are proven to improve execution stability, reports which ones are present and which are missing, and suggests how to strengthen them.

**It answers one question: "How likely is an AI agent to actually follow this skill's workflow, and what's missing that causes it to drift?"**

## The problem

You write a detailed SKILL.md: "first fetch the source, then download images, then write, then review." The AI says "got it" and skips to step 3.

This happens because LLMs:
- Have no persistent memory — every session starts fresh
- Follow instructions probabilistically, not deterministically
- Rationalize skipping steps — "this is simple, I don't need to fetch the source"

Community projects like [Superpowers](https://github.com/obra/superpowers) (24k+ stars) have developed techniques to solve this: anti-rationalization guards, stage gates, verification scripts, trap checklists. But most skill authors don't know which techniques their skill is missing — until the AI fails and they notice.

## What it checks

10 constraint dimensions, grouped by what they prevent:

| # | Constraint | What breaks without it |
|---|-----------|----------------------|
| 1 | Anti-rationalization guard | AI invents excuses to skip steps |
| 2 | Stage gates | AI runs through the whole workflow without pausing for confirmation |
| 3 | Automated verification | AI self-reports "passed" without actually checking |
| 4 | Decision flowchart | AI gets lost in vague natural-language instructions |
| 5 | Trap checklist | AI repeats known mistakes others already documented |
| 6 | Progressive disclosure | Context window overloaded, AI ignores key rules |
| 7 | Three-layer architecture | Skill tries to do everything, doesn't collaborate with other layers |
| 8 | Runtime hooks | Pure text instructions with no code-level enforcement |
| 9 | Context Engineering | Key rules buried in the middle, file too long |
| 10 | Scoped rules | All rules loaded all the time, including irrelevant ones |

**Not every skill needs all 10.** skill-auditor auto-detects the skill type and only evaluates applicable constraints:

| Skill type | Constraints checked | Example |
|------------|-------------------|---------|
| Workflow | 10/10 | Multi-step content creation, software processes |
| Tool | 7/10 | Script wrappers, CLI tools |
| Reference | 4/10 | Cheat sheets, API docs |
| Pattern | 3/10 | Methodologies, thinking frameworks |

## Who needs this

- **Skill authors**: Find weak spots before publishing — which missing constraint will cause the AI to drift?
- **Team leads**: Audit shared skills across a team's agent setup — are they robust enough for production use?
- **CI pipelines**: Quality gate — reject skills that lack critical constraints
- **AI users**: Evaluate third-party skills before installing — is this skill well-constrained or will it produce inconsistent results?

## What it does NOT do

- ❌ Does not check factual accuracy of skill content
- ❌ Does not fix skills — it reports weak spots, you strengthen them
- ❌ Does not guarantee AI compliance — constraint strength is one factor; model capability, context, and task complexity also matter
- ❌ Does not replace manual review — the script detects patterns objectively; supplement with subjective judgment (are the excuses specific enough? do traps have real test dates?)

## Install

```bash
npx skills add Foamtor/skill-auditor
```

Or:

```bash
git clone https://github.com/Foamtor/skill-auditor.git ~/.agents/skills/skill-auditor
```

Compatible with: Claude Code, OpenAI Codex, Cursor, Gemini CLI, Hermes Agent, and any tool that supports the [Agent Skills standard](https://agentskills.io).

## Usage

```bash
python3 scripts/audit_skill.py /path/to/SKILL.md

# JSON output for CI
python3 scripts/audit_skill.py /path/to/SKILL.md --json
```

### Exit codes

| Code | Meaning |
|------|---------|
| 0 | All required constraints present |
| 1 | Critical constraints missing |
| 2 | Parameter error |

## License

MIT
