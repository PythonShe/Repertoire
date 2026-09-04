# Repertoire

[English](README.md) | [简体中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | **日本語**

厳選された [Claude Code](https://code.claude.com/docs) Skill のコレクションで、単一のプラグインとして配布されています。一度インストールすればすべての Skill が利用可能です。

## インストール

```shell
/plugin marketplace add PythonShe/Repertoire
/plugin install repertoire@repertoire
```

インストール後、再起動するか `/reload-plugins` を実行してください。各 Skill は `/repertoire:<skill>` の名前空間で利用できます。

## 推奨セットアップ

これらの Skill は subagent の協調動作と推論トークンを多く活用します。以下の環境を推奨します。

- **Claude Max（5x または 20x）、もしくは API 課金**：推奨。軽量なプランでは利用上限に早く達する可能性があります。
- **OpenAI [`codex-cc` プラグイン](https://github.com/openai/codex-plugin-cc)**（任意）：対応 Skill でクロスモデルレビューを有効化します。未導入時は自動で Claude のみのレビューにフォールバックします。
- **GitHub [`gh-stack` 拡張](https://github.com/github/gh-stack)**（任意）：Maestro で複数計画のプランセットを GitHub の stacked PR として連携できます。
- **モデル選択**：セッションは `/model best` または `opus` で開始してください（**Sonnet は非推奨**）。ビルドタスクはセッションモデルを継承し、レビュータスクは Opus に固定されます。

## Skills

| Skill | コマンド | 機能説明 |
| :---- | :------- | :------- |
| **Eureka** | `/repertoire:eureka` | 構造化された対話を通じてプロジェクトのアイデアを発掘・精査し、優先順位付けされた候補リストを作成します。 |
| **Libretto** | `/repertoire:libretto` | 大まかな構想を、実装に直結する完全な技術仕様書（spec）へと昇華させます。 |
| **Score** | `/repertoire:score` | 技術仕様書を具体的な実装計画に分解します（大規模プロジェクト向けの複数計画セットにも対応）。 |
| **Maestro** | `/repertoire:maestro` | 実装計画をエンドツーエンドで指揮し、タスク実装、コードレビュー、品質検証を実行します。 |
| **Coda** | `/repertoire:coda` | オープンな PR のレビュー指摘や CI 失敗を精査し、修正から返信準備までを完結させます。 |
| **Encore** | `/repertoire:encore` | 既存のコードベースやモジュールを監査し、パフォーマンス、セキュリティ、品質の改善を抽出・実装します。 |
| **Presto** | `/repertoire:presto` | 小規模で明確な変更（150 行以内）のための高速レーン。事前の仕様書や計画作成なしに迅速に実装します。 |
| **Jam** | `/repertoire:jam` | 無人で自律動作し、リポジトリ内の実用的な改善点やバグ修正を発見・実装・検証します。 |
| **Legato** | `/repertoire:legato` | プロの動態標準に照らし合わせ、指定した UI のアニメーションやトランジションを監査・調整します。 |
| **Tuner** | `/repertoire:tuner` | 複雑なバグの根本原因を特定し、テスト駆動（TDD）で確実な修正を適用します。 |

## ローカル開発

クローンしたリポジトリから直接変更をテストできます。

```shell
claude --plugin-dir .
/reload-plugins
```

## リポジトリ構成

```text
Repertoire/                       リポジトリルート = プラグインルート = marketplace ルート
├── .claude-plugin/
│   ├── plugin.json               プラグインマニフェスト
│   └── marketplace.json          marketplace カタログエントリ
├── skills/                       各 Skill ディレクトリ
│   └── <name>/                   eureka、libretto、score、maestro、coda、encore、tuner、presto、jam、legato
├── shared/                       共有 subagent 規約とポリシー
├── docs/                         アーキテクチャ決定記録と作成指針
├── CHANGELOG.md
└── README.md
```

## 新しい Skill の追加

1. `skills/<name>/SKILL.md` を作成し（YAML フロントマターに `name`、`description`、`when_to_use` を指定）、`skills/<name>/evals/evals.json` にトリガー評価を追加します。
2. 上記の **Skills** テーブルに行を追加します。
3. 検証してコミットします。
   ```shell
   claude plugin validate .
   ```

既存のユーザーに変更を反映させる場合は、`.claude-plugin/plugin.json` と `.claude-plugin/marketplace.json` の両方で `version` を上げてください。
