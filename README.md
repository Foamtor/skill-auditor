# skill-auditor

> Detect and score SKILL.md instruction-following quality. Auto-detects skill type, selects applicable dimensions, outputs objective audit report.

## What is this?

An [Agent Skills](https://agentskills.io) that audits other SKILL.md files against 10 evidence-based techniques for making AI follow workflows reliably. Based on [Superpowers](https://github.com/obra/superpowers) (24k+ stars), the Agent Skills standard, and 3 months of production content creation experience.

## Why?

Writing a SKILL.md is easy. Getting AI to actually follow it is hard. LLMs tend to skip steps, rationalize shortcuts, and self-report "passed" without checking. This skill provides **objective, script-based detection** of whether a SKILL.md has the patterns that make AI comply.

## Install

```bash
# Via npx (recommended)
npx skills add Foamtor/skill-auditor

# Or clone directly
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

### Example output

```
## Skill审阅报告：ai-frontier-notes
类型：工作流型（检测到步骤流程）
适用维度：10/10

| # | 维度 | 评分 | 必须 | 说明 |
|---|------|------|------|------|
| 1 | 反合理化守卫 | ✅ | ✅ | 检测到5个反合理化模式 |
| 2 | 阶段门禁 | ❌ | ✅ | 未检测到阶段门禁 |
| 3 | 自动化验证 | ✅ | ✅ | 有scripts/目录且要求运行验证 |
| ... | ... | ... | ... | ... |

总分：5✅ 3⚠️ 2❌（共10项）

### 改进建议（按优先级排序）
1. **[最高]** 阶段门禁：未检测到阶段门禁
2. **[最高]** Context Engineering：文件47858B过大
```

### Exit codes

| Code | Meaning |
|------|---------|
| 0 | All required dimensions pass |
| 1 | One or more required dimensions fail |
| 2 | Parameter error |

## How it works

### Auto type detection

Not all 10 dimensions apply to every skill. A 200-word reference skill shouldn't get a ❌ for "no stage gates". The script auto-detects skill type first:

| Type | Applicable dims | Example |
|------|----------------|---------|
| Workflow | 10/10 | ai-frontier-notes, ruanzhu-from-scratch |
| Tool | 7/10 | skill-auditor itself |
| Reference | 4/10 | popular-web-designs |
| Pattern | 3/10 | objective-analysis |

### 10 audit dimensions

| # | Dimension | What it checks |
|---|-----------|---------------|
| 1 | Anti-rationalization guard | Lists excuses AI uses to skip steps |
| 2 | Stage gates | Hard stop points requiring human confirmation |
| 3 | Automated verification | Executable scripts that verify compliance |
| 4 | Decision flowchart | Explicit branching logic, not vague "then" |
| 5 | Trap checklist | Known errors from real-world testing, with dates |
| 6 | Progressive disclosure | Layered loading: triggers → details → references |
| 7 | Three-layer architecture | Integration with AGENTS.md and MCP |
| 8 | Runtime hooks | Code-level enforcement (pre-execution) |
| 9 | Context Engineering | Key rules front-loaded, total size reasonable |
| 10 | Scoped rules | Conditional loading by context |

### Objective + Subjective

The script provides **objective detection** (keyword patterns, file structure, size checks). For a complete audit, an AI agent should supplement with **subjective judgment** (are the excuses specific enough? do traps have real test dates? does the flowchart cover all branches?).

## 10 techniques article

For the full methodology behind these 10 dimensions, see the companion article: [让AI遵守你的工作流程：10个从实战中提炼的Skill设计技巧](https://github.com/Foamtor/skill-auditor/blob/main/article.md)

## License

MIT
