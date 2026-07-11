# Repertoire

[English](README.md) | **简体中文** | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md)

一套不断壮大的自研 [Claude Code](https://code.claude.com/docs) skill 合集（**repertoire** 意为"曲目库"），以单个插件的形式分发。安装一次即可获得全部 skill——随着合集的增长，你还会获得更多。

## 安装

```shell
/plugin marketplace add PythonShe/Repertoire
/plugin install repertoire@repertoire
```

然后重启或运行 `/reload-plugins`。skill 会以插件名作为命名空间，例如 `/repertoire:maestro`。

## 推荐配置

这些 skill 在设计上就是高 token 消耗的：每次运行都会调度一组全新的、以 medium 至 xhigh 推理强度运行的 subagent 协同工作——构建席位（实现者、修复者、QC 闸门）跑在你的会话模型上，评审席位（调查者、评审小组、校验者）固定用 Opus——而它们的可靠性正源于此。请相应地规划你的账户：

- **Claude Max（5x 或 20x），或 API 计费**——这是预期配置。更轻量的套餐很可能在运行途中就触及用量上限。
- **安装了 OpenAI [`codex-cc` 插件](https://github.com/openai/codex-plugin-cc)的 Codex 账户**——强烈建议。每个 skill 都会通过它调度一个跨模型的 Codex agent（评审者或调查者）。没有 Codex 时 skill 也能优雅降级——它们会改跑纯 Opus 的小组，并在报告中说明这一点——但跨模型校验本身就是设计的一部分。

> **模型说明：** 这些 skill 分两档运行。**构建席位**——实现者、修复者以及 QC／最终闸门——不固定模型：它们继承你的会话模型，所以请用你能访问到的最强模型来开会话。`/model best` 在你有权限时解析为 **Fable 5**，否则解析为最新的 **Opus**——既在构建席位上用上 Fable 的速度，又自带 Opus 回退，且不写死任何模型名，即便 Fable 再次被暂停也不会失效。**评审席位**——调查者、评审小组、校验者——固定用 **Opus**，作为稳定的对抗性基线。请用 `best` 或 `opus` 运行这些 skill——**绝不要用 Sonnet**。

## Skills

| Skill | Invoke | 功能 |
| :---- | :----- | :----------- |
| **Eureka** | `/repertoire:eureka` | 在动工之前先寻找值得做的想法：先确立一个谈话范围（一个现有代码库、一个开放领域，或某个半成形灵感的周边），再选定聚焦维度，然后通过一场有节奏的对话来发掘候选想法——种子草图、一块持续维护的想法看板、以及按需召唤的 3-4 个多视角创想者（ideator）集群——收敛出入围想法后，再把它们送入一条串行的审查漏斗（身份契合 → 合规 → 需求 → 可行性），其中每一个"淘汰"裁决都必须经用户确认后，想法才真正出局。产出止步于一份经过审查、排好序的想法清单，并可选地把冠军想法交给 Libretto——绝不写 spec，绝不写代码。在匹配的请求上自动触发。 |
| **Libretto** | `/repertoire:libretto` | 通过引导式的设计对话，把一个粗略的想法变成可直接开发的 spec，随后用一个对抗性的 subagent 评审小组（2-3 个多视角的 Opus 质疑者 + 一个跨模型的 Codex 评审者）对其加固，最后经过一道用户审批闸门。它只委派上下文收集和评审；产出止步于一份获批的 spec，并指向 Maestro 去构建它——绝不自动衔接。在匹配的请求上自动触发。 |
| **Score** | `/repertoire:score` | 把一份获批的 spec 转化为一份决策完备的实现计划——由若干 Maestro 可直接执行的任务组（乐章）构成，包含精确的路径、接口和测试预期，但不含函数体——随后用同样的对抗性评审小组对其加固，最后经过一道用户审批闸门。当一个目标需要多份计划时，会升级为计划集（plan set）：由控制器亲自撰写承载跨计划契约的 `00-overview.md`，再由并行的 plan-writer subagent 各写一份计划文件（单份计划不生成 overview）。与用户只设一个结构性检查点；产出止步于一份获批的计划，并指向 Maestro 去执行它——绝不自动衔接。在匹配的请求上自动触发。 |
| **Maestro** | `/repertoire:maestro` | 指挥由 subagent 驱动的实现计划执行：将相关任务分组，用全新的实现者构建每个任务组，然后把整个分支交由一个对抗性评审小组（3 个多视角的 Opus 质疑者 + 一个跨模型的 Codex 评审者）以及一道基于证据的质量管控（QC）合并闸门把关——与此同时指挥者自身的上下文保持精简。在匹配的请求上自动触发；未被明确点名时，会先确认范围与成本再进行调度。 |
| **Coda** | `/repertoire:coda` | 把一个开放 PR 的评审反馈处理到收尾：一个文书 agent（clerk）汇总每一条评审、行内讨论串、对话评论和失败的 CI 检查；一个只读的校验者把每一项与代码库的真实情况逐一比对（修复、反驳，还是发问——由证据决定）；多个串行的修复者修补存活下来的项；体量大或风险高的修复会面对一个 3 视角的 Opus 小组，每次运行都由一道分阶段的最终裁决封盘——先是基于证据的 QC（完整读取每一处修复），待 QC 通过后再由跨模型的 Codex 对整个 PR 走一遍；一道审批闸门同时覆盖推送以及发布草拟好的讨论串回复。绝不合并，绝不解决讨论串。在匹配的请求上自动触发；未被明确点名时，会先确认范围与成本再进行调度。 |
| **Encore** | `/repertoire:encore` | 回访一个已完成的代码库、特性或模块，为既有之物精益求精：一个侦察兵先为目标画像并提议一组质量视角（性能、安全、健壮性、DX 等），多个并行的多视角 Opus 猎手找出增强机会，再由一个只读的校验者把每一项主张与代码库的真实情况逐一比对——被驳回的项就此记录在案，特性级体量的项则转交 Libretto 或 Score，绝不在本次运行中实现。只有用户在"返场曲目单"闸门上选中的项才会被实现：在全新的 `encore/` 分支上由串行的修复者完成；体量大或风险高的改动会面对一个 3 视角的 Opus 小组，每次运行都由分阶段的最终裁决封盘——先是基于证据的 QC，通过后再由跨模型的 Codex 走一遍。推送与开 PR 都在同一道审批闸门之后，绝不合并。在匹配的请求上自动触发。 |
| **Tuner** | `/repertoire:tuner` | 用两个相互竞争的调查者把 bug 追到根因——一个在 bug 简报一就绪就被调度、以 xhigh 推理强度运行的 Codex agent，以及一个由分诊侦察兵排好序的故障面所引导、同样以 xhigh 推理强度运行的系统化 Opus 调查者——然后把这两个假设作为置信度闸门进行交叉质询。修复是测试先行的：修复者先提交一个故意失败（红）的复现测试，再做最小化的修复，一个持怀疑态度的评审者为修复把关，一个机械化的校验者证明从红到绿（red→green）以及整个测试套件为绿。在特性分支上提交，绝不合并。在匹配的请求上自动触发；未被明确点名时，会先确认范围与成本再进行调度。 |

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
│   └── <name>/                   eureka、libretto、score、maestro、coda、encore、tuner
│       ├── SKILL.md
│       ├── evals/evals.json      已提交的 trigger evals
│       ├── *-template.md         内置的文档结构（libretto、score）
│       └── *-prompt.md           内置的 subagent prompt 模板
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

当你希望已安装的用户拾取改动时，提升 `version`——在 `.claude-plugin/plugin.json` 和 `.claude-plugin/marketplace.json` 中的插件条目里设为**相同的值**（用户在执行 `/plugin marketplace update` 后才会看到它）。
