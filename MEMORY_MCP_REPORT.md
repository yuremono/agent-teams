# Memory MCP 利用可能化調査報告書

**作成日**: 2026-02-09
**担当**: general（汎用作業エージェント）
**タスクID**: #1, #3

---

## 1. 調査の背景・目的

### 背景
Memory MCP（`@modelcontextprotocol/server-memory@2026.1.26`）は以下の状態であった：
- パッケージはグローバルにインストール済み
- `~/.claude/settings.json` に `mcpServers` 設定が記述されていた
- しかし、MCPツールとして利用できていなかった

### 目的
- Memory MCPが利用できない原因を特定する
- 解消手順を明確にする
- ユーザーが行うべき次の行動を提示する

---

## 2. 原因

### 根本原因
**`~/.claude/settings.json` に `mcpServers` 設定を記述していても、CLI側のMCP登録（`claude mcp add`）が別途必要だった**

### 詳細
Claude Code CLIのMCP設定には、2つの異なる設定箇所が存在する：

| 設定箇所 | ファイル | 役割 | 設定方法 |
|---------|---------|------|----------|
| **プロジェクト設定** | `~/.claude/settings.json` | プロジェクトごとのMCPサーバー設定 | 手動でJSON記述 |
| **CLI全体設定** | `~/.claude.json` | CLI全体のMCPサーバー登録 | `claude mcp add`コマンド |

**重要**: `claude mcp list` で表示されるのは `~/.claude.json` に登録されたサーバーのみ。

### 動作確認
```bash
$ claude mcp list
No MCP servers configured. Use `claude mcp add` to add a server.
```

この結果により、`settings.json` の設定だけでは不十分であることが判明した。

---

## 3. 実施した解消手順

### 手順1: MCPサーバーの追加
```bash
claude mcp add memory -- npx -y @modelcontextprotocol/server-memory@2026.1.26
```

### 実行結果
```
Added stdio MCP server memory with command: npx -y @modelcontextprotocol/server-memory@2026.1.26 to local config
File modified: /Users/yanoseiji/.claude.json
```

### 手順2: 接続確認
```bash
$ claude mcp list

Checking MCP server health...

memory: npx -y @modelcontextprotocol/server-memory@2026.1.26 - ✓ Connected
```

**結果**: MCPサーバーは正常に接続されている。

---

## 4. 設定ファイルの変化

### ~/.claude.json（追加後の抜粋）
```json
{
  "projects": {
    "/Users/yanoseiji/Desktop/0209agent-teams": {
      "mcpServers": {
        "memory": {
          "type": "stdio",
          "command": "npx",
          "args": [
            "-y",
            "@modelcontextprotocol/server-memory@2026.1.26"
          ],
          "env": {}
        }
      }
    }
  }
}
```

---

## 5. ユーザーが行うべき次の行動

### 重要: セッションの再開が必要

現在のセッションはMCP追加前に開始されたため、**Memory MCPのツールは利用できません**。

### 手順

1. **現在のセッションを終了する**
   - エディタを閉じる、または `/exit` を実行

2. **新しいセッションを開始する**
   - プロジェクトディレクトリで `claude` コマンドを実行

3. **MCPツールの利用を確認する**
   - セッション開始後、以下のツールが利用可能になる：
     - `mcp__memory__read_graph` - グラフの読み取り
     - `mcp__memory__add_observations` - 観察データの追加
     - など

### トラブルシューティング

#### MCPサーバーが接続されているか確認
```bash
claude mcp list
```
`✓ Connected` と表示されていれば正常です。

#### MCPサーバーの削除・再登録が必要な場合
```bash
# 削除
claude mcp remove memory

# 再登録
claude mcp add memory -- npx -y @modelcontextprotocol/server-memory@2026.1.26
```

---

## 6. まとめ

| 項目 | 状態 |
|------|------|
| 原因特定 | ✅ 完了 |
| 解消手順の実行 | ✅ 完了 |
| サーバー接続確認 | ✅ 正常に接続中 |
| セッションでのツール利用 | ⚠️ セッション再開が必要 |

**結論**: Memory MCPサーバーは正常に登録・接続されています。次回のセッション開始時より、Memory MCPのツールが利用可能になります。
