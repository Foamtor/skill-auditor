# skill-auditor

> Check whether a SKILL.md has the anti-loss mechanisms that prevent AI from dropping steps and constraints during execution.

[English](README.md) | [中文](README_CN.md)

## What is this?

**A reliability checker for AI agent skill files.**

You install a SKILL.md, give it to an AI agent, and the results are inconsistent: sometimes the AI follows the workflow precisely, sometimes it skips steps, sometimes it drops constraints you thought were clear. The root cause is that the skill lacks mechanisms to prevent the AI from losing track of its own instructions.

skill-auditor scans a SKILL.md file for 10 known anti-loss patterns, reports which ones are present and which are missing, and suggests what to add.

**It answers one question: "Will this skill's workflow survive contact with an AI agent, or will key steps and constraints get dropped?"**

## The problem

LLMs don't have persistent memory. Every session starts fresh. When an AI agent reads a SKILL.md, it processes the instructions probabilistically — it might follow all steps, or it might skip some, or it might rationalize doing less work than specified.

Over time, the community has discovered specific patterns that reduce this drift: anti-rationalization guards that list excuses the AI will try to use, stage gates that force the AI to stop and wait for confirmation, verification scripts that catch "I'm done" claims that aren't actually true. Projects like [Superpowers](https://github.com/obra/superpowers) (24k+ stars) have formalized many of these.

But there's no quick way to check: **does this skill have those anti-loss mechanisms, or is it relying on the AI's good behavior?**

## Who uses this

- **Skill users (primary)**: You installed a third-party skill and the execution is inconsistent. Run skill-auditor to see what anti-loss mechanisms are missing.
- **Skill authors**: Before publishing, verify your skill has the patterns that make AI comply.
- **Team leads**: Audit shared skills before rolling them out to the team.
- **CI pipelines**: Quality gate — reject skills that lack critical anti-loss mechanisms.

## What it checks

10 anti-loss dimensions. Each one prevents a specific type of execution drift:

| # | Anti-loss mechanism | What gets dropped without it |
|---|-------------------|----------------------------|
| 1 | Anti-rationalization guard | Steps — AI invents excuses to skip them |
| 2 | Stage gates | Confirmations — AI runs through without pausing |
| 3 | Automated verification | Quality — AI self-reports "passed" without checking |
| 4 | Decision flowchart | Branching — AI gets lost in vague instructions |
| 5 | Trap checklist | Knowledge — AI repeats known mistakes |
| 6 | Progressive disclosure | Key rules — context overload causes AI to ignore them |
| 7 | Three-layer architecture | Context — skill tries to hold everything |
| 8 | Runtime hooks | Enforcement — pure text has no code-level backup |
| 9 | Context Engineering | Priority — key rules buried in long files |
| 10 | Scoped rules | Relevance — irrelevant rules dilute attention |

Not every skill needs all 10. skill-auditor auto-detects the skill type:

| Type | Mechanisms checked | Example |
|------|-------------------|---------|
| Workflow | 10/10 | Multi-step pipelines, content creation |
| Tool | 7/10 | Script wrappers, CLI tools |
| Reference | 4/10 | Cheat sheets, lookups |
| Pattern | 3/10 | Methodologies, frameworks |

## What it does NOT do

- ❌ Does not check factual accuracy
- ❌ Does not fix skills — it reports gaps, you fill them
- ❌ Does not guarantee compliance — anti-loss mechanisms reduce drift but can't eliminate it entirely
- ❌ Does not replace manual review — the script detects mechanism presence; whether the mechanism content is strong enough requires human judgment

## Install

```bash
npx skills add Foamtor/skill-auditor
```

Or:

```bash
git clone https://github.com/Foamtor/skill-auditor.git ~/.agents/skills/skill-auditor
```

## Usage

```bash
python3 scripts/audit_skill.py /path/to/SKILL.md
python3 scripts/audit_skill.py /path/to/SKILL.md --json
```

Exit codes: 0 = all critical mechanisms present, 1 = critical mechanisms missing, 2 = parameter error.

## License

MIT
