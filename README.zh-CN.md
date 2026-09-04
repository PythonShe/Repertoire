# Repertoire

[English](README.md) | **简体中文** | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md)

一套不断扩充的高质量 [Claude Code](https://code.claude.com/docs) Skill 合集（**Repertoire**，意为“技能库/曲目库”），以单插件形式分发。一次安装即可使用全部 Skill，后续扩充随更新即刻可用。

## 安装

```shell
/plugin marketplace add PythonShe/Repertoire
/plugin install repertoire@repertoire
```

然后重启或运行 `/reload-plugins`。Skill 会以插件名作为命名空间，例如 `/repertoire:maestro`。

## 推荐配置

这些 Skill 专为深度推理设计，每次运行均会调度多个专门的 subagent 在 medium 至 xhigh 推理强度下协同工作：构建角色（实现者、修复者、QC 质量把关）使用当前会话模型，评审角色（调查者、评审小组、校验者）固定使用 Opus。推荐配置如下：

- **Claude Max（5x 或 20x）或 API 计费**——推荐配置。轻量套餐可能在深度执行过程中触及用量限额。
- **配置了 OpenAI [`codex-cc` 插件](https://github.com/openai/codex-plugin-cc) 的 Codex 账户**——推荐配置。大多数 Skill 会通过该插件调度跨模型的 Codex agent（作为评审者或调查者）进行对抗性校验。如未配置 Codex，各 Skill 会平滑降级为纯 Opus 评审小组并在报告中予以注明。（Presto 与 Legato 刻意省略 Codex 评审，以保障局部改动的执行速度；Jam 采取全程无人值守运行，为避免外部调用挂起阻塞会话，采用 Opus 与会话模型的双重评审组合代替 Codex）。若缺少插件配套脚本，CLI 回退调用会加入标准输入重定向（`< /dev/null`）、文件输入输出及执行超时控制，避免进程挂起（参见 [openai/codex#20919](https://github.com/openai/codex/issues/20919) 与 `shared/codex-reviewer-core.md`）。
- **安装了 GitHub [`gh-stack` 扩展](https://github.com/github/gh-stack) 的 `gh` CLI**——可选。支持 Maestro 将多计划的 `stacked chain`（堆叠链）计划集自动关联为 GitHub stacked pull request。未安装时，平滑降级为指向父分支的标准推送。

> **模型说明：** 各 Skill 采用两档模型策略。**构建角色**（实现者、修复者及 QC 闸门）不固定模型，继承你的当前会话模型。建议使用最强可用模型启动会话：在具备访问权限时 `/model best` 会自动解析为 **Fable 5**，否则解析为最新的 **Opus**。**评审角色**（调查者、评审小组、校验者）统一固定为 **Opus**，提供稳定的对抗性基线（Jam 终审的第二位评审者使用会话模型，详见 `shared/invariants.md`）。请使用 `best` 或 `opus` 运行这些 Skill，**切勿使用 Sonnet**。

## Skills

| Skill | Invoke | 功能 |
| :---- | :----- | :----------- |
| **Eureka** | `/repertoire:eureka` | 在开发前发掘并评估项目构想。先确立探讨范围与聚焦维度，通过结构化对话、灵感看板以及多视角的创意生成 subagent 探索候选想法；随后将入围者送入递进式验证漏斗（定位契合、合规性、实际需求、技术可行性），任何淘汰判定均须经用户确认。最终产出一份排序后的评估清单并可将首选项转交 Libretto，不生成 spec 或代码。匹配请求时自动触发。 |
| **Libretto** | `/repertoire:libretto` | 通过引导式设计对话将粗略构想转化为完备的技术规范（spec）。规范在通过用户最终审批前，由对抗性 subagent 评审小组（2–3 位不同维度的 Opus 评审者 + 跨模型 Codex 评审者）进行全面检验。协调者保持自身上下文精炼，专注于对话与综合权衡。产出获批规范后指引由 Maestro 或 Score 接手。匹配请求时自动触发。 |
| **Score** | `/repertoire:score` | 将获批的 spec 转化为完备的实现计划，包含精确的文件路径、接口定义与测试预期。对于较复杂的任务，Score 可扩展为多计划集：由协调者编写包含跨计划契约的 `00-overview.md`，并调度并行 subagent 分别编写具体计划。overview 中可指定执行形态（`parallel` 并行分支，或通过 GitHub stacked PR 关联的 `stacked chain` 堆叠链）。最终交付获批计划并交由 Maestro 执行。匹配请求时自动触发。 |
| **Maestro** | `/repertoire:maestro` | 编排并调度 subagent 执行实现计划：将任务合理分组，指派全新实现者逐组构建，并由对抗性评审小组（3 位多视角 Opus 评审者 + 跨模型 Codex 评审者）以及基于客观事实的质量管控（QC）闸门把关整个分支。针对 `stacked chain` 计划集，单次运行执行一份计划并自动将依赖分支关联为 GitHub stacked PR，合并操作始终由用户手动完成。匹配请求时自动触发；未显式点名时会在启动前确认范围与成本。 |
| **Coda** | `/repertoire:coda` | 闭环处理未合并 PR 的评审反馈与 CI 失败：汇总所有评审意见、行内讨论串、评论与测试报错；由只读校验者对照代码库实际情况逐条核实（决定修复、反驳或发问）；按序实施修复并经 QC 检查与跨模型 Codex 评审验证。推送分支及发布讨论串回复合并为单道用户审批。绝不自动合并 PR，绝不擅自标记讨论串已解决。匹配请求时自动触发；未显式点名时会在启动前确认范围与成本。 |
| **Encore** | `/repertoire:encore` | 对已完成的代码库、功能或模块进行专项优化：先由探查者分析现状并制定质量维度（性能、安全性、稳定性、开发者体验等），并行 subagent 挖掘潜在改进点，再由只读校验者结合代码库核实可行性。仅有用户在交互选单中勾选的条目才会在独立的 `encore/` 分支上实施修复，并通过 QC 与跨模型 Codex 评审验证。在用户确认后提交 PR，绝不自动合并。匹配请求时自动触发。 |
| **Presto** | `/repertoire:presto` | 面向单次会话内快速交付的轻量通道，适用于明确的小型改动，无需前置编写 spec 或多阶段计划。由 Opus 探查者勘察相关代码并提出实现方案供用户确认；小型改动（≤150 行，≤3 个文件）由协调者直接完成以减少交接开销，较复杂改动交由实现者 subagent 处理。最后由独立 Opus 评审者与构建/测试 QC 闸门把关。若范围超出预期或 QC 连续未过，平滑转交 Libretto、Score 或 Maestro。不设 Codex 评审以优化小改动响应速度。匹配请求时自动触发；未显式点名时会在探查前确认改动需求与分支。 |
| **Jam** | `/repertoire:jam` | 全自主的无人值守开发会话：自主发掘仓库改进点、完成构建并验证交付。三个并行的 Opus 探查者从终端用户视角分析代码库（向后兼容的新功能、隐蔽缺陷修复、UI/UX 细节打磨）；随后由遴选者梳理出适合单次会话的 3–5 项任务清单。全新实现者按序构建任务并进行审查，对无法通过的改动执行干净的回滚（revert）而非停滞不前。最后通过分支双重评审（Opus 搭配当前会话模型）与严格的构建/测试 QC 验证。未选入或暂缓的候选项会整理至 `docs/repertoire/jam-backlog.md` 供后续会话参考。仅在请求时推送，绝不自动合并。匹配请求时自动触发；未显式点名时会在启动前确认范围与成本。 |
| **Legato** | `/repertoire:legato` | 专注于 UI 动效与微交互打磨的专用通道。由 Opus 探查者对照 Emil Kowalski 的专业动效标准（以 MIT 协议内置于 `references/`）审计指定页面或组件，提供包含精确参数的动效方案。用户在交互环节选定方向后，由实现者实施改动，独立 Opus 评审者对照核心动效准则审查，最后通过构建与测试验证（应用运行时可配合浏览器检查）。完成时将规范写入项目的 `docs/repertoire/animation.md` 作为长效参考。全局性重构请使用 Libretto、Score 与 Maestro。匹配请求时自动触发；未显式点名时会在探查前确认需求与分支。 |
| **Tuner** | `/repertoire:tuner` | 定位并修复复杂 Bug 的根因排查工具：并行调度 Codex 与 Opus 调查者交叉验证故障根因假说。严格采用测试驱动的修复流程：先提交复现问题的失败测试用例，再实施针对性最小修复，经独立评审审查并由自动化校验确认测试由红转绿（red → green）及全量测试通过。在特性分支上提交，绝不自动合并。匹配请求时自动触发；未显式点名时会在启动前确认范围与成本。 |

## 本地开发

无需安装，直接从克隆的仓库测试改动：

```shell
claude --plugin-dir .
/reload-plugins        # after edits, no restart needed
claude plugin validate .
```

## 仓库结构

```text
Repertoire/                       仓库根目录 = 插件根目录 = marketplace 根目录
├── .claude-plugin/
│   ├── plugin.json               插件清单（name: repertoire）
│   └── marketplace.json          列出本插件的 catalog（source "./"）
├── skills/                       每个 skill 一个目录
│   └── <name>/                   eureka、libretto、score、maestro、coda、encore、tuner、presto、jam、legato
│       ├── SKILL.md
│       ├── evals/evals.json      已提交的 trigger evals
│       ├── *-template.md         内置的文档结构（libretto、score、legato）
│       ├── *-prompt.md           内置的 subagent prompt 模板
│       └── references/           内置的第三方参考材料（legato；MIT 许可，见该目录的 LICENSE）
├── shared/
│   ├── codex-reviewer-core.md    共享的 Codex 调用契约
│   └── invariants.md             两档模型策略的权威定义
├── docs/
│   ├── adr/                      架构决策记录（ADR）
│   └── authoring/                skill 撰写准则
├── CHANGELOG.md
└── README.md
```

## 添加新 Skill

1. 创建 `skills/<name>/SKILL.md`（包含 YAML frontmatter：`name`、`description`、`when_to_use`——撰写准则见 `docs/authoring/skill-descriptions.md`）。把任何配套的 prompt/参考文件一并放在它旁边，并把 trigger evals 放在 `skills/<name>/evals/evals.json`。
2. 在上方的 **Skills** 表格中加一行。
3. 运行 `claude plugin validate .` 然后提交。新 skill 会被自动发现——整个仓库就是一个插件，所以 catalog 永远不需要新增条目。

当你希望已安装的用户获取更新时，提升 `version`——在 `.claude-plugin/plugin.json` 和 `.claude-plugin/marketplace.json` 中的插件条目里设为**相同的值**（用户在执行 `/plugin marketplace update` 后才会看到）。
