# Repertoire

[English](README.md) | [简体中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | **日本語**

自作の [Claude Code](https://code.claude.com/docs) Skill を集めた、成長し続ける**レパートリー**で、単一のプラグインとして配布されています。一度インストールすれば、すべての Skill が手に入ります。コレクションが拡大するにつれて、さらに多くの Skill が利用できるようになります。

## インストール

```shell
/plugin marketplace add PythonShe/Repertoire
/plugin install repertoire@repertoire
```

その後、再起動するか `/reload-plugins` を実行してください。Skill はプラグインの名前空間の下に置かれます（例: `/repertoire:maestro`）。

## 推奨セットアップ

これらの Skill は意図的にトークンを多く消費します。実行のたびに新規の Fable 5 subagent 群（実装者、調査者、レビューパネル、QC ゲート）を high の推論労力で編成して動かしており、その点こそが信頼性の源泉となっています。プランはそれに見合ったものを用意してください。

- **Claude Max（5x または 20x）、もしくは API 課金** — 想定されている構成です。これより軽量なプランでは、実行の途中で利用上限に達する可能性が高くなります。
- **OpenAI の [`codex-cc` プラグイン](https://github.com/openai/codex-plugin-cc) をインストールした Codex アカウント** — 強く推奨します。各 Skill は、これを通じてクロスモデルの Codex エージェント（レビュアーまたは調査者）をディスパッチします。Codex がなくても Skill は適切にデグレードして動作し、Fable 5 のみのパネルで実行したうえでその旨をレポートに記載しますが、クロスモデルでのチェックは設計の一部です。

## Skills

| Skill | Invoke | What it does |
| :---- | :----- | :----------- |
| **Eureka** | `/repertoire:eureka` | 何かを作り始める前に、作る価値のあるアイデアを発掘します。まず対話の範囲（既存のコードベース、オープンな領域、または半分形になったひらめきの周辺）とフォーカスする次元を定め、ペースを管理した対話で候補を探します。シードスケッチ、継続的に維持されるアイデアボード、そしてオンデマンドで編成される 3〜4 名の多様なレンズを持つ Fable 5 アイディエーター（ideator）のバーストです。ファイナリストを収束させたら、逐次的な審査ファネル（アイデンティティ → コンプライアンス → 需要 → 実現可能性）にかけます。どのキル評決も、ユーザーの確認があって初めてアイデアの死が確定します。成果物はランク付けされ審査済みのショートリストで、チャンピオンは任意で Libretto に引き継ぎます。spec もコードも書きません。手動専用で、名前を指定して呼び出します。 |
| **Libretto** | `/repertoire:libretto` | ガイド付きの設計対話を通じて、漠然としたアイデアをビルド可能な仕様に変え、最終的なユーザー承認ゲートの前に、敵対的な subagent のレビューパネル（多様なレンズを持つ 2〜3 名の Fable 5 懐疑者 + クロスモデルの Codex レビュアー）で堅牢化します。委譲するのはコンテキスト収集とレビューのみで、承認された仕様で完了し、それを構築するための Maestro を案内します。自動連鎖はしません。手動専用で、名前を指定して呼び出します。 |
| **Score** | `/repertoire:score` | 承認された仕様を、意思決定が完了した実装計画に変えます。Maestro がそのまま扱えるタスクグループの楽章（movement）として、正確なパス、インターフェース、テストの期待値を備えつつ、関数本体は含めません。その後、最終的なユーザー承認ゲートの前に、同じ敵対的なレビューパネルで堅牢化します。ユーザーとの構造的なチェックポイントは 1 回で、承認された計画で完了し、それを指揮するための Maestro を案内します。自動連鎖はしません。手動専用で、名前を指定して呼び出します。 |
| **Maestro** | `/repertoire:maestro` | 実装計画の subagent 駆動の実行を指揮します。関連するタスクをグループ化し、各グループを新規の実装者で構築したうえで、ブランチ全体を敵対的なレビューパネル（多様なレンズを持つ 3 名の Fable 5 懐疑者 + クロスモデルの Codex レビュアー）と、エビデンスに基づく品質管理のマージゲートでゲートします。その間、指揮者は自身のコンテキストを軽量に保ちます。手動専用で、名前を指定して呼び出します。 |
| **Coda** | `/repertoire:coda` | オープンな PR のレビューフィードバックを最後までやり切ります。事務担当（clerk）がすべてのレビュー、インラインスレッド、会話コメント、失敗した CI チェックを収集し、1 名の読み取り専用の検証者が各項目をコードベースの実態と照らし合わせて検証します（修正するか、押し返すか、質問するか — 判断はエビデンスが決めます）。逐次的に動くフィクサーが残った項目を修復し、大きい修正やリスクのある修正は 3 レンズの Fable 5 パネルにかけられます。すべての実行は段階的な最終評決で締めくくられます。これは各修正を全文読むエビデンスベースの QC で、QC をクリアすると PR 全体に対するクロスモデルの Codex パスが 1 回走ります。1 つの承認ゲートが、プッシュとドラフトされたスレッド返信の投稿の両方をカバーします。マージは行わず、スレッドの解決（resolve）も行いません。手動専用で、名前を指定して呼び出します。 |
| **Encore** | `/repertoire:encore` | 完成済みのコードベース、機能、モジュールを再訪し、すでに動いているものをさらに磨き上げます。スカウトがターゲットをプロファイルして品質レンズの編成（パフォーマンス、セキュリティ、堅牢性、DX など）を提案し、並列で動く多様なレンズの Fable 5 ハンターが強化の機会をコールし、1 名の読み取り専用の検証者がすべてのコールをコードベースの実態と照らし合わせます。反証されたコールは記録に残して棄却し、フィーチャー規模のものは Libretto または Score にルーティングして、この実行では決して実装しません。実装されるのは、セットリストのゲートでユーザーが選んだものだけです。新規の `encore/` ブランチ上で逐次的なフィクサーが実装し、大きい変更やリスクのある変更は 3 レンズの Fable 5 パネルにかけられ、すべての実行は段階的な最終評決 — エビデンスベースの QC、その通過後に 1 回のクロスモデルの Codex パス — で締めくくられます。プッシュと PR の作成は 1 つの承認ゲートの先にあり、マージは行いません。手動専用で、名前を指定して呼び出します。 |
| **Tuner** | `/repertoire:tuner` | 2 名のライバル調査者とともに、バグをその根本原因まで追い詰めます。1 名はバグのブリーフが存在した瞬間にディスパッチされる xhigh 推論労力の Codex エージェント、もう 1 名はトリアージのスカウトがランク付けした障害箇所で準備された、同じく xhigh の推論労力で動く体系的な Fable 5 調査者です。そして 2 つの仮説を突き合わせて反対尋問し、確信度ゲートとします。修復はテストファーストで進めます。フィクサーが、最小限の修正の前に意図的にレッドな再現テストをコミットし、懐疑的なレビュアーが修正をゲートし、機械的な検証者がレッド→グリーンとグリーンなテストスイートを証明します。フィーチャーブランチにコミットし、マージは行いません。手動専用で、名前を指定して呼び出します。 |

## ローカル開発

クローンから直接、インストールせずに変更をテストします。

```shell
claude --plugin-dir .
/reload-plugins        # after edits, no restart needed
claude plugin validate .
```

## リポジトリ構成

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

## 新しい Skill の追加

1. `skills/<name>/SKILL.md` を作成します（YAML フロントマター: `name`、`description`）。サポート用のプロンプト/リファレンスファイルがあれば、その隣にまとめて置きます。
2. 上記の **Skills** テーブルに行を追加します。
3. `claude plugin validate .` を実行してからコミットします。新しい Skill は自動的に検出されます。リポジトリ全体が 1 つのプラグインなので、カタログに新しいエントリは一切必要ありません。

`version` を上げます。**`.claude-plugin/plugin.json` と `.claude-plugin/marketplace.json` 内のプラグインエントリの両方**で同じ値にします。既存のインストールに変更を反映させたいときに行ってください（ユーザー側には `/plugin marketplace update` 実行後に反映されます）。
