# Repertoire

[English](README.md) | [简体中文](README.zh-CN.md) | **繁體中文** | [日本語](README.ja.md)

一套持續成長的自製 [Claude Code](https://code.claude.com/docs) skill
**曲目庫**（repertoire），以單一外掛的形式發行。安裝一次，就能取得所有 skill；
隨著收藏擴增，還會有更多 skill 加入。

## 安裝

```shell
/plugin marketplace add PythonShe/Repertoire
/plugin install repertoire@repertoire
```

接著重新啟動，或執行 `/reload-plugins`。skill 都收斂在外掛的命名空間底下，
例如 `/repertoire:maestro`。

## 建議的環境設定

這些 skill 刻意設計成相當耗用 token：每次執行都會調度一組全新的 Opus subagent
組成的陣容（實作者、調查員、審查小組、QC 把關），它們的可靠度正是來自於此。
請依此選擇方案：

- **Claude Max（5x 或 20x），或採用 API 計費** —— 這是預期的配置。較輕量的方案
  很可能在執行到一半時就撞上用量上限。
- **安裝了 OpenAI [`codex-cc` 外掛](https://github.com/openai/codex-plugin-cc)
  的 Codex 帳號** —— 強烈建議。每個 skill 都會透過它調度一個跨模型的 Codex agent
  （審查員或調查員）。沒有 Codex 時 skill 仍能優雅降級——它們會改跑純 Opus 的小組，
  並在報告中說明——但跨模型檢查本來就是設計的一部分。

## Skills

| Skill | Invoke | 功能說明 |
| :---- | :----- | :----------- |
| **Eureka** | `/repertoire:eureka` | 在動工之前先找出值得做的想法：先確立一個談話範圍（既有的程式碼庫、一個開放領域，或某個半成形靈感的周邊），再選定聚焦維度，接著透過一場有節奏的對話來發掘候選想法——種子草圖、一塊持續維護的想法看板、以及隨需召喚的 3-4 位多元視角 Opus 創想者（ideator）齊發——收斂出入圍想法之後，讓它們通過一條循序的審核漏斗（身分契合 → 法遵 → 需求 → 可行性），其中每一個「淘汰」裁決都必須經使用者確認後，想法才真正出局。產出止於一份經過審核、排好序的想法清單，並可選擇把冠軍想法交給 Libretto——絕不寫 spec，也絕不寫程式碼。僅限手動觸發；請以名稱呼叫它。 |
| **Libretto** | `/repertoire:libretto` | 透過一場引導式的設計對話，把一個粗略的想法轉化成可直接動工的 spec，接著在最終的使用者核可關卡之前，用一個對抗式 subagent 審查小組（2-3 個多元視角的 Opus 質疑者 + 一個跨模型的 Codex 審查員）來加以打磨。它只委派情境蒐集與審查工作；產出止於一份核可的 spec，並指向 Maestro 來實作它——絕不自動串接。僅限手動觸發；請以名稱呼叫它。 |
| **Score** | `/repertoire:score` | 把一份核可的 spec 轉化成一份決策完整的實作計畫——由可供 Maestro 接手的任務群「樂章」組成，附上精確的路徑、介面與測試預期，但不含函式主體——接著在最終的使用者核可關卡之前，用同一個對抗式審查小組加以打磨。與使用者只有一個結構性檢查點；產出止於一份核可的計畫，並指向 Maestro 來指揮它——絕不自動串接。僅限手動觸發；請以名稱呼叫它。 |
| **Maestro** | `/repertoire:maestro` | 指揮一份實作計畫的 subagent 驅動執行：把相關任務分組，用一個全新的實作者建立每一組，接著讓整個分支通過一個對抗式審查小組（3 個多元視角的 Opus 質疑者 + 一個跨模型的 Codex 審查員）與一道以證據為本的品質管控合併關卡——而指揮者本身則維持精簡的情境。僅限手動觸發；請以名稱呼叫它。 |
| **Coda** | `/repertoire:coda` | 把一個開啟中的 PR 的審查回饋處理到收尾：一位書記彙整每一則審查、行內討論串、對話留言與失敗的 CI 檢查；一個唯讀的驗證者拿每一項與程式碼的實際狀況對照測試（修正、反駁，或詢問——由證據決定）；接著由循序的修正者修復存活下來的項目；較大或較有風險的修正會面對一個 3 視角的 Opus 小組，而每次執行都以分階段的最終裁決封存——一道以證據為本的 QC 會完整讀過每一項修正，待 QC 通過後再對整個 PR 跑一輪跨模型的 Codex；單一核可關卡即涵蓋推送與張貼草擬好的討論串回覆。絕不合併，也絕不關閉討論串。僅限手動觸發；請以名稱呼叫它。 |
| **Encore** | `/repertoire:encore` | 回訪一個已完成的程式碼庫、功能或模組，把既有的東西打磨得更好：一位偵察員先為目標建立輪廓並提議一組品質視角（效能、安全、強健性、DX 等），多位並行的多元視角 Opus 獵手找出增強機會，再由一個唯讀的驗證者把每一項主張與程式碼的實際狀況逐一比對——被駁回的項目就此留下紀錄，功能級規模的項目則轉交 Libretto 或 Score，絕不在本次執行中實作。只有使用者在「安可曲目單」關卡選中的項目才會被實作：在全新的 `encore/` 分支上由循序的修正者完成；較大或較有風險的變更會面對一個 3 視角的 Opus 小組，每次執行都以分階段的最終裁決封存——先是以證據為本的 QC，通過後再跑一輪跨模型的 Codex。推送與開 PR 都在同一道核可關卡之後，絕不合併。僅限手動觸發；請以名稱呼叫它。 |
| **Tuner** | `/repertoire:tuner` | 用兩位彼此競爭的調查員把一個 bug 追到根因——一個是在 bug 簡報一成形就立刻調度、以 xhigh 推理強度運作的 Codex agent，另一個則是由分流偵察員所排序的故障面向所啟動的系統化 Opus 調查員——接著交叉檢視這兩個假說作為信心關卡。修復採測試先行：修正者會先提交一個刻意為紅的重現測試，再做最小幅度的修正，由一位帶著質疑態度的審查員為修正把關，最後由機械式的驗證者證明 red→green 並且整個測試套件為綠。在功能分支上提交，絕不合併。僅限手動觸發；請以名稱呼叫它。 |

## 本機開發

不必安裝，直接從複製出來的儲存庫測試變更：

```shell
claude --plugin-dir .
/reload-plugins        # after edits, no restart needed
claude plugin validate .
```

## 儲存庫結構

```text
Repertoire/                       repo root = plugin root = marketplace root
├── .claude-plugin/
│   ├── plugin.json               plugin manifest (name: repertoire)
│   └── marketplace.json          catalog listing this plugin (source "./")
├── skills/
│   ├── eureka/
│   │   ├── SKILL.md
│   │   └── *-prompt.md           bundled subagent prompt templates
│   ├── libretto/
│   │   ├── SKILL.md
│   │   ├── spec-template.md      bundled spec structure
│   │   └── *-prompt.md           bundled subagent prompt templates
│   ├── score/
│   │   ├── SKILL.md
│   │   ├── plan-template.md      bundled plan structure
│   │   └── *-prompt.md           bundled subagent prompt templates
│   ├── maestro/
│   │   ├── SKILL.md
│   │   └── *-prompt.md           bundled subagent prompt templates
│   ├── coda/
│   │   ├── SKILL.md
│   │   └── *-prompt.md           bundled subagent prompt templates
│   ├── encore/
│   │   ├── SKILL.md
│   │   └── *-prompt.md           bundled subagent prompt templates
│   └── tuner/
│       ├── SKILL.md
│       └── *-prompt.md           bundled subagent prompt templates
└── README.md
```

## 新增 Skill

1. 建立 `skills/<name>/SKILL.md`（含 YAML frontmatter：`name`、`description`）。
   把任何支援用的 prompt／參考檔案一併放在它旁邊。
2. 在上方的 **Skills** 表格新增一列。
3. 執行 `claude plugin validate .` 後再提交。新 skill 會被自動探索到——整個儲存庫
   就是一個外掛，因此目錄（catalog）永遠不需要新增條目。

當你希望既有的安裝端跟進這項變更時，把 `version` 升版——在
`.claude-plugin/plugin.json` 與 `.claude-plugin/marketplace.json` 裡的外掛
條目**兩處**填入相同的值（使用者在執行 `/plugin marketplace update` 之後才會看到）。
