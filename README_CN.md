# skill-auditor

[English](README.md) | 中文

> 评估SKILL.md对模型的约束强度。找出导致执行不稳定的薄弱环节，给出可操作的改进建议。

## 这是什么

**skill-auditor评估AI agent skill文件的约束强度。**

你写了一个SKILL.md交给AI执行，结果经常不稳定：有时AI严格按流程走，有时跳过步骤，有时合理化偷工减料。根本原因是大多数SKILL.md缺少让AI必须遵守的结构性约束。

skill-auditor扫描SKILL.md中的10个约束模式（这些模式已被验证能提升执行稳定性），报告哪些存在、哪些缺失，并建议如何加强。

**它回答一个问题："AI执行这个skill时，遵守流程的概率有多大？哪些缺失的约束会导致执行漂移？"**

## 解决什么问题

你写了一份详细的SKILL.md："先抓原文，再下配图，再写作，再审阅"。AI回复"好的"，然后直接跳到第三步。

这是因为大语言模型：
- 没有持久记忆，每次会话从零开始
- 对指令的遵循是概率性的，不是确定性的
- 会合理化跳过步骤，"这个很简单，不需要抓原文"

[Superpowers](https://github.com/obra/superpowers)（24k+ stars）等社区项目已经开发了应对技巧：反合理化守卫、阶段门禁、验证脚本、陷阱清单。但大多数skill作者不知道自己的skill缺了哪些技巧，直到AI出错才发现。

## 10个约束维度

按它们防止的问题分组：

| # | 约束维度 | 缺失会导致什么 |
|---|---------|--------------|
| 1 | 反合理化守卫 | AI编造借口跳过步骤 |
| 2 | 阶段门禁 | AI一口气跑完整个流程，不等确认 |
| 3 | 自动化验证 | AI自我报告"通过"而不实际检查 |
| 4 | 决策流程图 | AI在模糊的自然语言指令中迷失 |
| 5 | 陷阱清单 | AI重复别人已经记录过的已知错误 |
| 6 | 渐进式加载 | context window过载，AI忽略关键规则 |
| 7 | 三层架构 | 一个文件承载所有信息，不与其他层协作 |
| 8 | Runtime Hooks | 纯文本指令，没有代码层面的强制 |
| 9 | Context Engineering | 关键规则埋在中间，文件过长 |
| 10 | Scoped Rules | 所有规则始终加载，包括不相关的 |

**不是每个skill都需要10个。** skill-auditor自动判断skill类型，只评估适用的约束：

| Skill类型 | 检查的约束数 | 示例 |
|-----------|-------------|------|
| 工作流型 | 10/10 | 多步骤内容创作、软件开发流程 |
| 工具型 | 7/10 | 脚本封装、CLI工具 |
| 参考型 | 4/10 | 速查表、API文档 |
| 模式型 | 3/10 | 方法论、思维框架 |

## 给谁用

- **Skill作者**：发布前找出薄弱环节，哪个缺失的约束会导致AI执行漂移？
- **团队负责人**：审计团队的共享skill，约束强度够不够生产使用？
- **CI管线**：质量门禁，拒绝缺少关键约束的skill
- **AI用户**：安装第三方skill前评估质量，这个skill约束够强还是结果不稳定？

## 不做什么

- ❌ 不检查内容的事实准确性
- ❌ 不修复skill，只报告薄弱环节
- ❌ 不保证AI会遵守（约束强度是一个因素，模型能力、上下文、任务复杂度也有影响）
- ❌ 不替代人工审阅，脚本做客观检测，AI补充主观判断

## 安装

```bash
npx skills add Foamtor/skill-auditor
```

或直接clone：

```bash
git clone https://github.com/Foamtor/skill-auditor.git ~/.agents/skills/skill-auditor
```

兼容：Claude Code、OpenAI Codex、Cursor、Gemini CLI、Hermes Agent，以及所有支持[Agent Skills标准](https://agentskills.io)的工具。

## 使用

```bash
python3 scripts/audit_skill.py /path/to/SKILL.md

# JSON格式输出，适合CI
python3 scripts/audit_skill.py /path/to/SKILL.md --json
```

### 退出码

| 代码 | 含义 |
|------|------|
| 0 | 所有必须约束都存在 |
| 1 | 有关键约束缺失 |
| 2 | 参数错误 |

## 开源协议

MIT
