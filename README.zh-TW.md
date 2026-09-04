# Repertoire

[English](README.md) | [简体中文](README.zh-CN.md) | **繁體中文** | [日本語](README.ja.md)

精選的 [Claude Code](https://code.claude.com/docs) Skill 合集，以單一外掛形式發行。安裝一次即可使用全部 Skill。

## 安裝

```shell
/plugin marketplace add PythonShe/Repertoire
/plugin install repertoire@repertoire
```

安裝後重新啟動或執行 `/reload-plugins`。各 Skill 統一在 `/repertoire:<skill>` 命名空間底下。

## 建議配置

這些 Skill 深度依賴 subagent 協同與推理 tokens，建議配置如下：

- **Claude Max（5x 或 20x）或採用 API 計費**：推薦配置，輕量方案可能快速觸及用量上限。
- **OpenAI [`codex-cc` 外掛](https://github.com/openai/codex-plugin-cc)**（選配）：為支援的 Skill 啟用跨模型審查；未安裝時自動平滑降級為純 Claude 審查。
- **GitHub [`gh-stack` 擴充](https://github.com/github/gh-stack)**（選配）：允許 Maestro 將多計畫集關聯為 GitHub stacked PR。
- **模型建議**：使用 `/model best` 或 `opus` 啟動會話（**請勿使用 Sonnet**）。建置任務繼承會話模型，審查任務固定使用 Opus。

## Skills

| Skill | 喚起指令 | 功能說明 |
| :---- | :------- | :------- |
| **Eureka** | `/repertoire:eureka` | 透過結構化腦力激盪發掘並評估專案構想，產出依優先級排序的候選方案清單。 |
| **Libretto** | `/repertoire:libretto` | 將粗略想法打磨為完整、可直接落地的技術規格書（spec）。 |
| **Score** | `/repertoire:score` | 將技術規格書拆解為具體的實作計畫（大型專案支援生成多計畫集）。 |
| **Maestro** | `/repertoire:maestro` | 全流程編排執行實作計畫，負責任務實作、程式碼審查與品質驗證。 |
| **Coda** | `/repertoire:coda` | 閉環處理開啟中 PR 的審查回饋，修復審查意見與 CI 失敗項目。 |
| **Encore** | `/repertoire:encore` | 審查既有程式碼庫或功能模組，發掘並實施效能、安全性與程式碼品質改進。 |
| **Presto** | `/repertoire:presto` | 針對小型、明確變更（≤150 行）的輕量快速通道，無需前置撰寫 spec 或多階段計畫。 |
| **Jam** | `/repertoire:jam` | 無人值守的自主開發會話，自動發現、實作並驗證儲存庫中的實用改進與修復。 |
| **Legato** | `/repertoire:legato` | 對照專業動效標準，審計並打磨指定 UI 的動畫效果與微互動。 |
| **Tuner** | `/repertoire:tuner` | 診斷複雜 Bug 的根本原因，並以測試驅動方式實施精準修復。 |

## 本機開發

直接透過本機複製測試變更：

```shell
claude --plugin-dir .
/reload-plugins
```

## 儲存庫結構

```text
Repertoire/                       儲存庫根目錄 = 外掛根目錄 = marketplace 根目錄
├── .claude-plugin/
│   ├── plugin.json               外掛清單
│   └── marketplace.json          marketplace 目錄條目
├── skills/                       各 skill 目錄
│   └── <name>/                   eureka、libretto、score、maestro、coda、encore、tuner、presto、jam、legato
├── shared/                       共用 subagent 契約與策略
├── docs/                         架構決策紀錄與編寫指引
├── CHANGELOG.md
└── README.md
```

## 新增 Skill

1. 建立 `skills/<name>/SKILL.md`（在 YAML frontmatter 中宣告 `name`、`description` 與 `when_to_use`），並在 `skills/<name>/evals/evals.json` 中新增 trigger evals。
2. 在上方的 **Skills** 表格中新增一列。
3. 驗證並提交：
   ```shell
   claude plugin validate .
   ```

若要讓既有安裝的使用者取得更新，請同時在 `.claude-plugin/plugin.json` 與 `.claude-plugin/marketplace.json` 中提升 `version`。
