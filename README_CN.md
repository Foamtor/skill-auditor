# skill-auditor

[English](README.md) | 中文

> 检测SKILL.md的指令遵循质量。自动判断skill类型，基于适用维度逐项评分，输出客观审阅报告。

基于[Superpowers](https://github.com/obra/superpowers)（24k+ stars）、[Agent Skills标准](https://agentskills.io)和3个月生产环境实战经验，封装为可执行的检测工具。

## 为什么需要

写一个SKILL.md容易，让AI真的按它执行很难。LLM倾向于跳过步骤、合理化偷懒、自我报告"通过"而不实际检查。skill-auditor提供**基于脚本的客观检测**，判断SKILL.md是否包含让AI遵守流程的关键模式。

## 安装

```bash
# 通过 npx（推荐）
npx skills add Foamtor/skill-auditor

# 或直接 clone
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

### 输出示例

```
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

### 退出码

| 代码 | 含义 |
|------|------|
| 0 | 所有必须维度通过 |
| 1 | 有必须维度未通过 |
| 2 | 参数错误 |

## 工作原理

### 自动类型判断

不是所有10个维度都适用于每个skill。一个200字的参考型skill不该收到"没有阶段门禁"的❌。脚本先自动判断skill类型：

| 类型 | 适用维度数 | 示例 |
|------|-----------|------|
| 工作流型 | 10/10 | ai-frontier-notes, ruanzhu-from-scratch |
| 工具型 | 7/10 | skill-auditor自身 |
| 参考型 | 4/10 | popular-web-designs |
| 模式型 | 3/10 | objective-analysis |

### 10个检查维度

| # | 维度 | 检查什么 |
|---|------|---------|
| 1 | 反合理化守卫 | 是否列出AI跳过步骤的借口并逐一反驳 |
| 2 | 阶段门禁 | 是否有强制停止点要求人类确认 |
| 3 | 自动化验证 | 是否有可执行的验证脚本 |
| 4 | 决策流程图 | 是否有显式的流程分支而非模糊描述 |
| 5 | 陷阱清单 | 是否有带日期的已知错误列表 |
| 6 | 渐进式加载 | 是否有分层设计（触发条件→详细规范→参考资料） |
| 7 | 三层架构 | 是否与AGENTS.md和MCP协作 |
| 8 | Runtime Hooks | 是否有代码层面的事前拦截机制 |
| 9 | Context Engineering | 关键规则是否前置，总长度是否合理 |
| 10 | Scoped Rules | 是否支持条件加载 |

### 客观检测 + 主观判断

脚本提供**客观检测**（关键词模式、文件结构、大小检查）。完整的审阅还需要AI补充**主观判断**：借口列表是否具体、陷阱是否有实测案例、流程图是否覆盖所有分支。

## 开源协议

MIT
