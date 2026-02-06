# multi-agent-shogun システム構成

> **Version**: 2.0
> **Last Updated**: 2026-02-06

## 概要
multi-agent-shogunは、Claude Code + tmux を使ったマルチエージェント並列開発基盤である。
戦国時代の軍制をモチーフとした階層構造で、複数のプロジェクトを並行管理できる。

---

## 📁 コンテキストモジュール（ジャストインタイム読み込み）

詳細なシステムドキュメントはモジュール化されており、必要に応じて読み込むこと：

| モジュール | 内容 | 読み込むタイミング |
|-----------|------|-------------------|
| **context/system_architecture.md** | システム構造、階層、tmux設定 | システム全体像を把握したい時 |
| **context/workflow.md** | ワークフロー、復帰手順 | 作業手順が不明な時 |
| **context/protocols.md** | 通信プロトコル、send-keysルール | 通知・報告を行う時 |

---

## セッション開始時の必須行動（全エージェント必須）

新たなセッションを開始した際は、作業前に必ず以下を実行せよ：

1. **Memory MCPを確認**: `mcp__memory__read_graph` でルール・コンテキストを確認
2. **自分の役割のinstructionsを読む**:
   - 将軍 → instructions/shogun.md
   - 家老 → instructions/karo.md
   - 足軽 → instructions/ashigaru.md
3. **詳細が必要な場合は、各モジュールを読む**

---

## コンパクション復帰時（全エージェント必須）

1. **自分のIDを確認**: `tmux display-message -t "$TMUX_PANE" -p '#{@agent_id}'`
2. **対応するinstructionsを読む**
3. **禁止事項を確認してから作業開始**

> 正データは各YAMLファイル（queue/tasks/, queue/reports/）である。

---

## /clear後の復帰手順（足軽専用）

/clear を受けた足軽は、以下の手順で最小コストで復帰せよ。

### 復帰フロー

```
/clear実行
  │
  ▼ CLAUDE.md 自動読み込み（本セクションを認識）
  │
  ▼ Step 1: 自分のIDを確認
  │   tmux display-message -t "$TMUX_PANE" -p '#{@agent_id}'
  │   → 出力例: ashigaru3 → 自分は足軽3
  │
  ▼ Step 2: Memory MCP 読み込み（~700トークン）
  │   mcp__memory__read_graph()
  │
  ▼ Step 3: 自分のタスクYAML読み込み（~800トークン）
  │   queue/tasks/ashigaru{N}.yaml を読む
  │   → status: assigned なら作業再開
  │   → status: idle なら次の指示を待つ
  │
  ▼ Step 4: プロジェクト固有コンテキスト（条件必須）
  │   タスクYAMLに project フィールドがある場合 → context/{project}.md を読む
  │
  ▼ 作業開始
```

### /clear復帰の禁止事項
- instructions/ashigaru.md は読まなくてよい（2タスク目以降で必要なら読む）
- ポーリング禁止（F004）、人間への直接連絡禁止（F002）は引き続き有効

---

## 🚨 ファイル操作の鉄則

- **WriteやEditの前に必ずReadせよ。** Claude Codeは未読ファイルへのWrite/Editを拒否する。

---

## 通知ツール

足軽から家老への通知は、作成した `bin/notify-karo` スクリプトを使用すること：

```bash
bin/notify-karo "メッセージ内容"
```

または、従来の `tmux send-keys` を2回に分けて実行すること。

---

## 言語設定

config/settings.yaml の `language` で設定（戦国風日本語）。

---

## 関連ファイル

- **instructions/shogun.md** - 将軍の指示書
- **instructions/karo.md** - 家老の指示書
- **instructions/ashigaru.md** - 足軽の指示書
- **context/system_architecture.md** - システムアーキテクチャ詳細
- **context/workflow.md** - ワークフロー詳細
- **context/protocols.md** - 通信プロトコル詳細
