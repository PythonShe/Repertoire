# Repertoire

[English](README.md) | **简体中文** | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md)

精选的 [Claude Code](https://code.claude.com/docs) Skill 合集，以单一插件形式分发。安装一次即可使用全部 Skill。

## 安装

```shell
/plugin marketplace add PythonShe/Repertoire
/plugin install repertoire@repertoire
```

安装后重启或运行 `/reload-plugins`。各 Skill 统一在 `/repertoire:<skill>` 命名空间下。

## 推荐配置

这些 Skill 深度依赖 subagent 协同与推理 tokens，建议配置如下：

- **Claude Max（5x 或 20x）或 API 计费**：推荐配置，轻量套餐可能快速触及用量上限。
- **OpenAI [`codex-cc` 插件](https://github.com/openai/codex-plugin-cc)**（可选）：为支持的 Skill 启用跨模型审查；未安装时自动回退为纯 Claude 审查。
- **GitHub [`gh-stack` 扩展](https://github.com/github/gh-stack)**（可选）：允许 Maestro 将多计划集关联为 GitHub stacked PR。
- **模型建议**：使用 `/model best` 或 `opus` 启动会话（**请勿使用 Sonnet**）。构建任务继承会话模型，审查任务固定使用 Opus。

## Skills

| Skill | 唤起命令 | 功能说明 |
| :---- | :------- | :------- |
| **Eureka** | `/repertoire:eureka` | 通过结构化头脑风暴发掘并评估项目构想，产出优先级排序的候选方案清单。 |
| **Libretto** | `/repertoire:libretto` | 将粗略想法打磨为完整、可直接落地的技术规格书（spec）。 |
| **Score** | `/repertoire:score` | 将技术规格书拆解为具体的实施计划（大型项目支持生成多计划集）。 |
| **Maestro** | `/repertoire:maestro` | 全流程编排执行实施计划，负责任务实现、代码审查与质量验证。 |
| **Coda** | `/repertoire:coda` | 闭环处理开放中 PR 的评审反馈，修复审查意见与 CI 失败项。 |
| **Encore** | `/repertoire:encore` | 审查现有代码库或功能模块，发掘并实施性能、安全性与代码质量改进。 |
| **Presto** | `/repertoire:presto` | 针对小型、明确改动（≤150 行）的轻量快速通道，无需前置编写 spec 或多阶段计划。 |
| **Jam** | `/repertoire:jam` | 无人值守的自主开发会话，自动发现、实现并验证仓库中的实用改进与修复。 |
| **Legato** | `/repertoire:legato` | 对照专业动效标准，审计并打磨指定 UI 的动画效果与微交互。 |
| **Tuner** | `/repertoire:tuner` | 诊断复杂 Bug 的根本原因，并以测试驱动方式实施精准修复。 |

## 本地开发

直接通过本地克隆测试改动：

```shell
claude --plugin-dir .
/reload-plugins
```

## 仓库结构

```text
Repertoire/                       仓库根目录 = 插件根目录 = marketplace 根目录
├── .claude-plugin/
│   ├── plugin.json               插件清单
│   └── marketplace.json          marketplace 目录条目
├── skills/                       各 skill 目录
│   └── <name>/                   eureka、libretto、score、maestro、coda、encore、tuner、presto、jam、legato
├── shared/                       共享 subagent 契约与策略
├── docs/                         架构决策记录与编写指南
├── CHANGELOG.md
└── README.md
```

## 添加新 Skill

1. 创建 `skills/<name>/SKILL.md`（在 YAML frontmatter 中声明 `name`、`description` 和 `when_to_use`），并在 `skills/<name>/evals/evals.json` 中添加 trigger evals。
2. 在上方的 **Skills** 表格中添加一行。
3. 验证并提交：
   ```shell
   claude plugin validate .
   ```

若要让已有安装用户获取更新，请同时在 `.claude-plugin/plugin.json` 与 `.claude-plugin/marketplace.json` 中提升 `version`。
