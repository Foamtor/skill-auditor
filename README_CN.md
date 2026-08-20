# skill-auditor

[English](README.md) | 中文

> SKILL.md的代码检查工具。自动判断skill类型，基于10个维度评分，输出改进建议。

## 这是什么

**skill-auditor是SKILL.md文件的代码检查工具（linter）。**

就像ESLint检查JavaScript代码质量、Pylint检查Python一样，skill-auditor检查SKILL.md文件是否包含让AI真正遵守指令的结构性要素。

它回答一个问题：**这个SKILL.md有没有让AI遵守流程的关键模式，还是只是一段AI大概率会忽略的自然语言描述？**

## 解决什么问题

你写了一份详细的SKILL.md，告诉AI"先抓原文，再下配图，再写作，再审阅"。AI回复"好的"，然后直接跳到第三步。

这是因为大语言模型：
- 没有持久记忆，每次会话从零开始
- 对指令的遵循是概率性的，不是确定性的
- 会合理化跳过步骤，"这个很简单，不需要抓原文"

[Superpowers](https://github.com/obra/superpowers)（24k+ stars）等社区项目已经开发了应对技巧：反合理化守卫、阶段门禁、验证脚本、陷阱清单。但**没有方法知道你的SKILL.md是否用了这些技巧**，直到AI出错你才发现。

skill-auditor通过扫描SKILL.md中的10个经过验证的合规模式，给出评分报告来解决这个问题。

## 给谁用

- **Skill作者**：发布前检查SKILL.md是否包含让AI遵守的关键模式
- **团队负责人**：审计团队agent配置中的共享skill
- **CI管线**：作为质量门禁，拒绝缺少关键模式的skill
- **AI用户**：安装第三方skill前评估质量

## 10个检查维度

| # | 维度 | 缺失会导致什么 |
|---|------|--------------|
| 1 | 反合理化守卫 | AI会找借口跳过步骤 |
| 2 | 阶段门禁 | AI会一口气跑完整个流程不停下 |
| 3 | 自动化验证 | AI会自我报告"通过"而不实际检查 |
| 4 | 决策流程图 | AI会在模糊的"然后""如果"中迷失 |
| 5 | 陷阱清单 | AI会重复别人已经记录过的已知错误 |
| 6 | 渐进式加载 | context window过载，AI忽略关键规则 |
| 7 | 三层架构 | 一个文件承载所有信息，不与AGENTS.md/MCP协作 |
| 8 | Runtime Hooks | 纯文本指令，没有代码层面的强制 |
| 9 | Context Engineering | 关键规则埋在中间，文件过长 |
| 10 | Scoped Rules | 所有规则始终加载，即使不相关 |

**不是所有10个维度都适用于每个skill。** 一个简短的参考型skill不需要阶段门禁。skill-auditor自动判断skill类型，只检查适用维度：

| Skill类型 | 适用维度数 | 示例 |
|-----------|-----------|------|
| 工作流型 | 10/10 | 写作流水线、多步骤流程 |
| 工具型 | 7/10 | 脚本、CLI工具封装 |
| 参考型 | 4/10 | 速查表、API文档 |
| 模式型 | 3/10 | 方法论、思维框架 |

## 不做什么

- ❌ 不检查内容的事实准确性
- ❌ 不修复skill，只报告问题
- ❌ 不保证AI会遵守（合规取决于AI模型、上下文和任务）
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
# 审阅单个skill
python3 scripts/audit_skill.py /path/to/SKILL.md

# JSON格式输出（适合CI集成）
python3 scripts/audit_skill.py /path/to/SKILL.md --json
```

### 退出码

| 代码 | 含义 |
|------|------|
| 0 | 所有必须维度通过 |
| 1 | 有必须维度未通过 |
| 2 | 参数错误 |

在CI中使用，拒绝缺少关键模式的skill：

```bash
python3 scripts/audit_skill.py my-skill/SKILL.md || echo "Skill质量检查未通过"
```

## 开源协议

MIT
