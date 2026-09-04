# Repertoire

[English](README.md) | [简体中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | **日本語**

自作の [Claude Code](https://code.claude.com/docs) Skill を集めた、継続的に拡張される高品質な**レパートリー**で、単一のプラグインとして配布されています。一度インストールすればすべての Skill が利用可能となり、今後の拡張も更新を通じて即座に反映されます。

## インストール

```shell
/plugin marketplace add PythonShe/Repertoire
/plugin install repertoire@repertoire
```

その後、再起動するか `/reload-plugins` を実行してください。Skill はプラグインの名前空間の下に置かれます（例: `/repertoire:maestro`）。

## 推奨セットアップ

各 Skill は高度な推論を行うよう設計されており、実行ごとに複数の専門 subagent を medium〜xhigh の推論労力で協調動作させます。ビルドロール（実装者、修正者、QC ゲート）はセッションモデルを継承し、レビューロール（調査者、レビューパネル、検証者）は Opus に固定することで高い信頼性を担保しています。以下の環境を推奨します。

- **Claude Max（5x または 20x）、もしくは API 課金** — 推奨。軽量なプランでは実行途中で利用制限に達する可能性があります。
- **OpenAI の [`codex-cc` プラグイン](https://github.com/openai/codex-plugin-cc) を導入した Codex アカウント** — 推奨。ほとんどの Skill で独立したクロスモデルの Codex エージェント（レビュアーまたは調査者）による敵対的検証を行います。Codex が利用できない場合も、Opus のみのレビューパネルへ適切にフォールバックして実行されます。（Presto と Legato は局所的な変更の速度を優先して Codex レビューを省略し、Jam は無人実行が外部呼び出しで停止するのを防ぐため Opus とセッションモデルのペアで代替します）。プラグインの companion スクリプトがない場合、`codex` CLI のフォールバック呼び出しには標準入力のリダイレクト（`< /dev/null`）、ファイルベースの I/O、タイムアウト制御を組み込み、ハングを防止します（上流 issue [openai/codex#20919](https://github.com/openai/codex/issues/20919) および `shared/codex-reviewer-core.md` 参照）。
- **GitHub の [`gh-stack` 拡張](https://github.com/github/gh-stack) を導入した `gh` CLI** — 任意。Maestro が `stacked chain`（スタックチェーン）プランセットを GitHub の stacked pull request として自動連携できるようになります。未導入の場合は親ブランチをターゲットにした通常のプッシュにフォールバックします。

> **モデルに関する注記：** 2 階層のモデル構成を採用しています。**ビルドロール**（実装者、修正者、QC ゲート）はセッションモデルを継承します。`/model best` は利用可能であれば **Fable 5**、それ以外は最新の **Opus** に解決されます。**レビューロール**（調査者、レビューパネル、検証者）は、安定した検証品質を保つため **Opus** に固定されます（Jam の最終レビュー第 2 レビュアーのみセッションモデルを使用、`shared/invariants.md` 参照）。これらの Skill は `best` または `opus` で実行し、**Sonnet は使用しないでください**。

## Skills

| Skill | Invoke | 機能 |
| :---- | :----- | :--- |
| **Eureka** | `/repertoire:eureka` | 実装前にアイデアを発掘・評価します。探索範囲と着眼点を定めたうえで、対話、アイデアボード、多角的な視点を持つアイディエーター subagent を通じて候補を洗い出します。絞り込まれた候補は段階的な検証ファネル（整合性・コンプライアンス・需要・実現可能性）を通過し、いずれの却下判断もユーザーの確認を必須とします。最終的にランク付けされた候補リストを Libretto に引き継ぎます（spec やコードは作成しません）。条件に一致するリクエストで自動起動します。 |
| **Libretto** | `/repertoire:libretto` | ガイド付きの設計対話を通じて、大まかな構想を実装可能な詳細仕様書（spec）へと昇華させます。仕様は最終的なユーザー承認の前に、敵対的 subagent レビューパネル（多角的な Opus レビュアー 2〜3 名 + クロスモデル Codex レビュアー）によって精査されます。オーケストレーターは対話と統合に専念し、コンテキストを軽量に保ちます。承認された仕様を Maestro や Score に引き継ぎます。条件に一致するリクエストで自動起動します。 |
| **Score** | `/repertoire:score` | 承認された仕様を、正確なファイルパス・インターフェース・テスト条件を含む具体的な実装計画へと変換します。規模の大きい開発では、全体計画 `00-overview.md` と並列 subagent による個別計画ファイルからなるプランセットを構成します。overview では実行形態（並列ブランチ、または GitHub stacked PR で連携する `stacked chain`）を指定できます。敵対的レビューパネルの精査を経て、承認された計画を Maestro に引き継ぎます。条件に一致するリクエストで自動起動します。 |
| **Maestro** | `/repertoire:maestro` | 専門 subagent を指揮して実装計画を実行します。タスクを論理グループにまとめ、新規の実装者 subagent で順次構築し、ブランチ全体を敵対的レビューパネル（Opus 3 名 + Codex 1 名）と品質管理（QC）マージゲートで厳格に検証します。`stacked chain` プランセットでは計画ごとに実行し、依存ブランチを GitHub stacked PR として自動連携します（マージは常にユーザーの手動操作に委ねられます）。条件に一致するリクエストで自動起動します（明示的な指定がない場合は開始前に範囲とコストを確認）。 |
| **Coda** | `/repertoire:coda` | オープンな PR のレビュー指摘や CI テスト失敗を最後まで解決します。すべてのレビューコメントやインライン指摘、CI 失敗内容を収集し、読み取り専用の検証者が実コードと突き合わせて対応方針（修正・反論・確認）を判定します。修正は段階的な QC とクロスモデル Codex レビューで検証され、プッシュやコメント投稿は 1 つのユーザー承認ゲートで安全に管理されます。PR の自動マージやスレッドの無断解決は行いません。条件に一致するリクエストで自動起動します（明示的な指定がない場合は開始前に確認）。 |
| **Encore** | `/repertoire:encore` | 完成済みのコードベースや機能に対して、パフォーマンス・セキュリティ・堅牢性・開発者体験（DX）などの観点から追加の改善点を洗い出します。並列の Opus subagent が候補を抽出し、読み取り専用の検証者が実態を確認します。ユーザーが選択した項目のみを独立した `encore/` ブランチで修正し、QC とクロスモデルレビューで検証した上で PR を作成します（自動マージは行いません）。条件に一致するリクエストで自動起動します。 |
| **Presto** | `/repertoire:presto` | 単一セッションで完了する小規模で明確な変更のための高速レーンです。事前の spec や計画作成を省略し、Opus スカウトが調査して実装方針をユーザーに確認します。小規模な変更（目安 150 行以内、3 ファイル以内）は引き継ぎコストを省くためコントローラーが直接実装し、それ以上は実装者 subagent に委譲します。独立した Opus レビューと QC ゲートで品質を担保し、規模が超過した場合や QC に連続失敗した場合は Libretto、Score、Maestro に引き継ぎます。局所的な変更の速度を優先し Codex レビューは省略します。条件に一致するリクエストで自動起動します（明示的な指定がない場合は調査前に確認）。 |
| **Jam** | `/repertoire:jam` | リポジトリの改善を自律的に発見・実装・検証する無人セッションです。3 名の並列 Opus スカウトがエンドユーザー視点（後方互換の新機能、潜在バグの修正、UI/UX 改善）で調査し、セレクターが 1 セッション分の 3〜5 件のタスクを策定します。実装者が順次構築・レビューを行い、進行不能になった変更は停滞を避けてクリーンに revert します。Opus とセッションモデルによるブランチ全体の二重レビューと QC 検証を経て安全に完了します。見送られた候補は `docs/repertoire/jam-backlog.md` に保存され、次回セッションで再評価されます。指示された場合のみプッシュし、自動マージは行いません。条件に一致するリクエストで自動起動します（明示的な指定がない場合は事前に確認）。 |
| **Legato** | `/repertoire:legato` | 指定された UI のアニメーションやインタラクションを磨き上げる専用レーンです。Emil Kowalski 氏の動效標準（MIT ライセンスに基づき `references/` に同梱）に準拠し、Opus スカウトが対象コンポーネントを監査して正確なパラメータを提案します。ユーザーと方針を確定後、実装、Opus レビュー、QC テスト（起動中アプリのブラウザ確認対応）を実施します。決定事項は `docs/repertoire/animation.md` に永続的なガイドラインとして記録します。アプリ全体の刷新は Libretto、Score、Maestro に委ねます。条件に一致するリクエストで自動起動します（明示的な指定がない場合は事前に確認）。 |
| **Tuner** | `/repertoire:tuner` | バグの根本原因を特定し解決する専用ツールです。Codex と Opus の 2 つの調査エージェントが仮説を競合・検証して確実性を高めます。テスト駆動のアプローチを採用し、最小限の修正を行う前に必ず失敗する再現テストをコミットし、独立レビューとレッドからグリーン（red → green）への遷移を自動検証します。機能ブランチにコミットし、自動マージは行いません。条件に一致するリクエストで自動起動します（明示的な指定がない場合は事前に確認）。 |

## ローカル開発

クローンから直接、インストールせずに変更をテストします。

```shell
claude --plugin-dir .
/reload-plugins        # after edits, no restart needed
claude plugin validate .
```

## リポジトリ構成

```text
Repertoire/                       リポジトリルート = プラグインルート = marketplace ルート
├── .claude-plugin/
│   ├── plugin.json               プラグインマニフェスト（name: repertoire）
│   └── marketplace.json          このプラグインを掲載するカタログ（source "./"）
├── skills/                       Skill ごとに 1 ディレクトリ
│   └── <name>/                   eureka、libretto、score、maestro、coda、encore、tuner、presto、jam、legato
│       ├── SKILL.md
│       ├── evals/evals.json      コミット済みの trigger evals
│       ├── *-template.md         同梱のドキュメント構造（libretto、score、legato）
│       ├── *-prompt.md           同梱の subagent プロンプトテンプレート
│       └── references/           同梱のサードパーティ参照資料（legato。MIT ライセンス、同ディレクトリの LICENSE を参照）
├── shared/
│   ├── codex-reviewer-core.md    共有の Codex 呼び出し契約
│   └── invariants.md             規範となる 2 階層モデルポリシー
├── docs/
│   ├── adr/                      アーキテクチャ決定記録（ADR）
│   └── authoring/                Skill 作成の指針
├── CHANGELOG.md
└── README.md
```

## 新しい Skill の追加

1. `skills/<name>/SKILL.md` を作成します（YAML フロントマター: `name`、`description`、`when_to_use` — 指針は `docs/authoring/skill-descriptions.md` にあります）。サポート用のプロンプト/リファレンスファイルがあれば、その隣にまとめて置きます。あわせて、トリガー評価（trigger evals）を `skills/<name>/evals/evals.json` に置きます。
2. 上記の **Skills** テーブルに行を追加します。
3. `claude plugin validate .` を実行してからコミットします。新しい Skill は自動的に検出されます。リポジトリ全体が 1 つのプラグインなので、カタログに新しいエントリは一切必要ありません。

既存のインストールに変更を反映させたいときは、`version` を上げます。**`.claude-plugin/plugin.json` と `.claude-plugin/marketplace.json` 内のプラグインエントリの両方**で同じ値に設定してください（ユーザー側には `/plugin marketplace update` 実行後に反映されます）。
