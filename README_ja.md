# Agent Teams + tmux インフラ

<div align="center">

**Claude Code Agent Teams 統率システム**

*公式Agent Teams機能とtmux通知を組み合わせたマルチエージェント基盤*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude-Code-blueviolet)](https://claude.ai)
[![tmux](https://img.shields.io/badge/tmux-optional-green)](https://github.com/tmux/tmux)

[English](README.md) | [日本語](README_ja.md)

</div>

---

## これは何？

**Agent Teams + tmux インフラ** は、Claude Codeの公式Agent Teams機能とtmux通知を組み合わせたマルチエージェント並列開発基盤です。

**なぜ使うのか？**
- Agent Teams機能で柔軟なチーム編成
- tmuxで他セッション・ユーザーへの通知
- カスタムエージェント定義で役割分離
- Memory MCPでセッションを跨いだ知見蓄積

```
      あなた（ユーザー）
           │
           ▼ 指示
    ┌─────────────┐
    │  リーダー    │  ← チーム編成・タスク割り当て
    └──────┬──────┘
           │ Task tool
      ┌────▼────┐
      │  Agent  │  ← researcher, implementer, reviewer, general...
      │  Team   │
      └────┬────┘
           │
           ▼ tmux通知（オプション）
        他チーム・ユーザーへ通知
```

---

## アーキテクチャ

### フラット構造（チームベース）

| 役割 | 説明 |
|------|------|
| **リーダー** | チーム編成・タスク割り当て・進捗管理 |
| **researcher** | 調査・情報収集・分析 |
| **implementer** | 実装・コーディング |
| **reviewer** | レビュー・検証 |
| **general** | 汎用作業 |

### チーム編成例

| タイプ | 推奨メンバー |
|--------|-------------|
| 調査・分析 | researcher + general |
| 開発・実装 | implementer + reviewer |
| フルサイクル | researcher + implementer + reviewer + general |

---

## クイックスタート

### 前提条件

- [Claude Code](https://claude.ai) がインストールされている
- tmux がインストールされている（オプション）

### Step 1: リポジトリの取得

```bash
git clone <repository-url>
cd 0205multi-agent-shogun
```

### Step 2: セットアップ

```bash
# セットアップスクリプトの実行
./setup.sh
```

### Step 3: カスタムエージェントの確認

```bash
# ~/.claude/agents/ にエージェント定義があることを確認
ls ~/.claude/agents/
# researcher.md, implementer.md, reviewer.md, general.md 等
```

### Step 4: チームを作成してタスクを実行

Claude Code内で：

```python
# チームの作成
TeamCreate(
    team_name="my-project",
    description="My project team"
)

# メンバーの追加（並列実行可能）
Task(
    subagent_type="researcher",
    team_name="my-project",
    description="調査タスク",
    prompt="〜について調査してください"
)

Task(
    subagent_type="implementer",
    team_name="my-project",
    description="実装タスク",
    prompt="〜を実装してください"
)
```

---

## プロジェクト管理

プロジェクトは `WORKS/{MMDD}{ProjectName}/` 形式で管理します。

```
config/projects.yaml       # プロジェクト一覧
WORKS/                      # プロジェクトルート
WORKS/0209ExampleProject/   # 例: プロジェクト
  ├── project.yaml         # プロジェクト詳細
  ├── src/                 # ソースコード
  └── docs/                # 設計書等
```

---

## tmux連携（オプション）

Agent Teams内のエージェントからtmuxセッションの他ペインに通知を送る場合：

### send-keysルール

```bash
# 【1回目】メッセージを送る
tmux send-keys -t session:pane 'メッセージ内容'
# 【2回目】Enterを送る
tmux send-keys -t session:pane Enter
```

### 通知スクリプト

```bash
bin/notify-team "タスク完了"
```

---

## 設定

### config/settings.yaml

```yaml
language: standard  # standard（標準）, ja（戦国風）
```

### CLAUDE.md

プロジェクトルートの `CLAUDE.md` は**リーダー（メインエージェント）専用**です。

- チーム編成ルール
- tmux連携方法
- プロジェクト管理ルール

チームメンバーは `~/.claude/agents/{name}.md` を読んでください。

---

## MCPツール

導入済みMCP:
- **Memory**: セッションを跨いだ知見蓄積
- **Notion**: Notion連携
- **Playwright**: E2Eテスト
- **GitHub**: GitHub連携
- **Sequential Thinking**: 複雑な推論

---

## ディレクトリ構造

```
├── CLAUDE.md              # リーダー専用ルール
├── README.md              # このファイル
├── config/                # 設定ファイル
├── context/               # プロジェクト固有コンテキスト
├── bin/                   # ユーティリティスクリプト
├── WORKS/                 # プロジェクトディレクトリ（Git管理外）
└── .claude/               # プロジェクト固有のスキル/コマンド
```

---

## トラブルシューティング

詳細は [TROUBLESHOOTING.md](TROUBLESHOOTING.md) を参照してください。

---

## ライセンス

[MIT License](LICENSE)
