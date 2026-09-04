# Repertoire

[English](README.md) | [简体中文](README.zh-CN.md) | **繁體中文** | [日本語](README.ja.md)

一套持續擴充的高品質 [Claude Code](https://code.claude.com/docs) Skill 合集（**Repertoire**，意為「技能庫／曲目庫」），以單一外掛形式發行。安裝一次即可使用全部 Skill，後續擴充隨更新即刻可用。

## 安裝

```shell
/plugin marketplace add PythonShe/Repertoire
/plugin install repertoire@repertoire
```

接著重新啟動，或執行 `/reload-plugins`。Skill 都會收斂在外掛的命名空間底下，例如 `/repertoire:maestro`。

## 建議配置

這些 Skill 專為深度推理設計，每次執行均會調度多個專門的 subagent 在 medium 至 xhigh 推理強度下協同運作：建置角色（實作者、修復者、QC 品質把關）使用當前會話模型，審查角色（調查員、審查小組、驗證者）固定使用 Opus。建議配置如下：

- **Claude Max（5x 或 20x）或採用 API 計費**——推薦配置。較輕量的方案可能在深度執行途中觸及用量上限。
- **安裝了 OpenAI [`codex-cc` 外掛](https://github.com/openai/codex-plugin-cc) 的 Codex 帳號**——推薦配置。大多數 Skill 會透過該外掛調度跨模型的 Codex agent（作為審查員或調查員）進行對抗性檢驗。若未配置 Codex，各 Skill 會平滑降級為純 Opus 審查小組並在報告中說明。（Presto 與 Legato 刻意省略 Codex 審查，以確保局部小變更的執行速度；Jam 採取全程無人值守運作，為避免外部呼叫掛起而阻斷會話，採用 Opus 與會話模型的雙重審查組合代替 Codex）。若缺少外掛配套腳本，CLI 回退呼叫會加入標準輸入重新導向（`< /dev/null`）、檔案輸入輸出與執行逾時控制，避免行程卡死（請參閱 [openai/codex#20919](https://github.com/openai/codex/issues/20919) 與 `shared/codex-reviewer-core.md`）。
- **安裝了 GitHub [`gh-stack` 擴充](https://github.com/github/gh-stack) 的 `gh` CLI**——選配。支援 Maestro 將多計畫的 `stacked chain`（堆疊鏈）計畫集自動關聯為 GitHub stacked pull request。未安裝時，平滑降級為指向父分支的標準推送。

> **模型說明：** 各 Skill 採用兩檔模型策略。**建置角色**（實作者、修復者及 QC 關卡）不固定模型，繼承你的當前會話模型。建議使用最強可用模型啟動會話：在具備存取權限時 `/model best` 會自動解析為 **Fable 5**，否則解析為最新的 **Opus**。**審查角色**（調查員、審查小組、驗證者）統一固定為 **Opus**，提供穩定的對抗性基準（Jam 終審的第二位審查員使用會話模型，詳見 `shared/invariants.md`）。請使用 `best` 或 `opus` 執行這些 Skill，**切勿使用 Sonnet**。

## Skills

| Skill | Invoke | 功能說明 |
| :---- | :----- | :----------- |
| **Eureka** | `/repertoire:eureka` | 在動工前發掘並評估專案構想。先確立探討範圍與聚焦維度，透過結構化對話、靈感看板以及多視角的創意生成 subagent 探索候選想法；隨後將入圍者送入遞進式驗證漏斗（定位契合、合規性、實際需求、技術可行性），任何淘汰判定皆須經使用者確認。最終產出一份排序後的評估清單並可將首選項轉交 Libretto，不產生 spec 或程式碼。符合請求時自動觸發。 |
| **Libretto** | `/repertoire:libretto` | 透過引導式設計對話將粗略構想轉化為完備的技術規格（spec）。規格在通過使用者最終審批前，由對抗式 subagent 審查小組（2–3 位不同維度的 Opus 審查員 + 跨模型 Codex 審查員）進行全面檢驗。協調者保持自身情境精簡，專注於對話與綜合權衡。產出獲批規格後指引由 Maestro 或 Score 接手。符合請求時自動觸發。 |
| **Score** | `/repertoire:score` | 將獲批的 spec 轉化為完備的實作計畫，包含精確的檔案路徑、介面定義與測試預期。針對較複雜的工作，Score 可擴展為多計畫集：由協調者撰寫包含跨計畫契約的 `00-overview.md`，並調度平行 subagent 分別撰寫具體計畫。overview 中可指定執行形態（`parallel` 平行分支，或透過 GitHub stacked PR 關聯的 `stacked chain` 堆疊鏈）。最終交付獲批計畫並交由 Maestro 執行。符合請求時自動觸發。 |
| **Maestro** | `/repertoire:maestro` | 編排並調度 subagent 執行實作計畫：將任務合理分組，指派全新實作者逐組建置，並由對抗式審查小組（3 位多視角 Opus 審查員 + 跨模型 Codex 審查員）以及基於客觀事實的品質管控（QC）關卡把關整個分支。針對 `stacked chain` 計畫集，單次執行處理一份計畫並自動將依賴分支關聯為 GitHub stacked PR，合併操作始終由使用者手動完成。符合請求時自動觸發；未明確點名時會在啟動前確認範圍與成本。 |
| **Coda** | `/repertoire:coda` | 閉環處理開啟中 PR 的審查回饋與 CI 失敗：彙整所有審查意見、行內討論串、留言與測試失敗；由唯讀驗證者對照程式碼庫實際狀況逐條核實（決定修正、反駁或發問）；依序實施修復並經 QC 檢查與跨模型 Codex 審查驗證。推送分支及發布討論串回覆合併為單道使用者審批關卡。絕不自動合併 PR，絕不擅自標記討論串已解決。符合請求時自動觸發；未明確點名時會在啟動前確認範圍與成本。 |
| **Encore** | `/repertoire:encore` | 對已完成的程式碼庫、功能或模組進行專項最佳化：先由探查者分析現狀並提議品質維度（效能、安全性、強健性、開發者體驗等），平行 subagent 挖掘潛在改進點，再由唯讀驗證者結合程式碼庫核實可行性。僅有使用者在互動選單中勾選的項目才會在獨立的 `encore/` 分支上實施修復，並通過 QC 與跨模型 Codex 審查驗證。在使用者確認後提交 PR，絕不自動合併。符合請求時自動觸發。 |
| **Presto** | `/repertoire:presto` | 面向單次會話內快速交付的輕量通道，適用於明確的小型變更，無需前置撰寫 spec 或多階段計畫。由 Opus 探查者勘察相關程式碼並提出實作方案供使用者確認；小型變更（≤150 行，≤3 個檔案）由協調者直接完成以減少交接開銷，較複雜變更交由實作者 subagent 處理。最後由獨立 Opus 審查員與建置/測試 QC 關卡把關。若範圍超出預期或 QC 連續未過，平滑轉交 Libretto、Score 或 Maestro。不設 Codex 審查以最佳化小變更回應速度。符合請求時自動觸發；未明確點名時會在探查前確認變更需求與分支。 |
| **Jam** | `/repertoire:jam` | 全自主的無人值守開發會話：自主發掘儲存庫改進點、完成建置並驗證交付。三位平行的 Opus 探查者從終端使用者視角分析程式碼庫（向後相容的新功能、隱蔽缺陷修復、UI/UX 細節打磨）；隨後由遴選者整理出適合單次會話的 3–5 項任務清單。全新實作者依序建置任務並進行審查，對無法通過的變更執行乾淨的復原（revert）而非停滯不前。最後透過分支雙重審查（Opus 搭配當前會話模型）與嚴格的建置/測試 QC 驗證。未選入或暫緩的候選項會整理至 `docs/repertoire/jam-backlog.md` 供後續會話參考。僅在要求時推送，絕不自動合併。符合請求時自動觸發；未明確點名時會在啟動前確認範圍與成本。 |
| **Legato** | `/repertoire:legato` | 專注於 UI 動態效果與微互動打磨的專用通道。由 Opus 探查者對照 Emil Kowalski 的專業動效標準（以 MIT 授權內建於 `references/`）審計指定頁面或元件，提供包含精確參數的動效方案。使用者在互動環節選定方向後，由實作者實施變更，獨立 Opus 審查員對照核心動效準則審查，最後透過建置與測試驗證（應用程式執行時可配合瀏覽器檢查）。完成時將規範寫入專案的 `docs/repertoire/animation.md` 作為長效參考。全域性重構請使用 Libretto、Score 與 Maestro。符合請求時自動觸發；未明確點名時會在探查前確認需求與分支。 |
| **Tuner** | `/repertoire:tuner` | 定位並修復複雜 Bug 的根本原因排查工具：平行調度 Codex 與 Opus 調查員交叉驗證故障假說。嚴格採用測試驅動的修復流程：先提交重現問題的失敗測試，再實施針對性最小修復，經獨立審查員審查並由自動化驗證確認測試由紅轉綠（red → green）及全量測試通過。在功能分支上提交，絕不自動合併。符合請求時自動觸發；未明確點名時會在啟動前確認範圍與成本。 |

## 本機開發

不必安裝，直接從複製出來的儲存庫測試變更：

```shell
claude --plugin-dir .
/reload-plugins        # after edits, no restart needed
claude plugin validate .
```

## 儲存庫結構

```text
Repertoire/                       儲存庫根目錄 = 外掛根目錄 = marketplace 根目錄
├── .claude-plugin/
│   ├── plugin.json               外掛清單（name: repertoire）
│   └── marketplace.json          列出本外掛的 catalog（source "./"）
├── skills/                       每個 skill 一個目錄
│   └── <name>/                   eureka、libretto、score、maestro、coda、encore、tuner、presto、jam、legato
│       ├── SKILL.md
│       ├── evals/evals.json      已提交的 trigger evals
│       ├── *-template.md         內建的文件結構（libretto、score、legato）
│       ├── *-prompt.md           內建的 subagent prompt 範本
│       └── references/           內建的第三方參考資料（legato；MIT 授權，見該目錄的 LICENSE）
├── shared/
│   ├── codex-reviewer-core.md    共用的 Codex 呼叫契約
│   └── invariants.md             兩檔模型策略的權威定義
├── docs/
│   ├── adr/                      架構決策紀錄（ADR）
│   └── authoring/                skill 撰寫準則
├── CHANGELOG.md
└── README.md
```

## 新增 Skill

1. 建立 `skills/<name>/SKILL.md`（含 YAML frontmatter：`name`、`description`、
   `when_to_use`——撰寫準則見 `docs/authoring/skill-descriptions.md`）。
   把任何支援用的 prompt／參考檔案一併放在它旁邊，並把 trigger evals 放在
   `skills/<name>/evals/evals.json`。
2. 在上方的 **Skills** 表格新增一列。
3. 執行 `claude plugin validate .` 後再提交。新 skill 會被自動探索到——整個儲存庫
   就是一個外掛，因此目錄（catalog）永遠不需要新增條目。

當你希望既有的安裝端取得更新時，把 `version` 升版——在
`.claude-plugin/plugin.json` 與 `.claude-plugin/marketplace.json` 裡的外掛
條目**兩處**填入相同的值（使用者在執行 `/plugin marketplace update` 之後才會看到）。
