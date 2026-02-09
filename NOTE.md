# Memory MCP 環境整備 - 作業メモ

日時: 2026-02-09

## 状況

### 完了した作業
- Agent Teams インフラの環境整備完了
  - skills/, logs/, projects/ ディレクトリ作成
  - config/settings.yaml のパス更新
  - language: standard に変更
  - memory/global_context.md 更新

### Memory MCP の状態
- **既に設定済み**: `~/.claude/settings.json` に `@modelcontextprotocol/server-memory@2026.1.26` が設定されている
- **動作確認**: "Knowledge Graph MCP Server running on stdio"
- **保存場所**: `~/.claude/memory/` （ローカル、外部サービス非依存）
- **問題点**: ツールリストに `mcp__memory__read_graph` 等が表示されない

### Agent Teams の問題
- チーム作成は成功するが、エージェントが tmux の別ペインに表示されない
- TaskList にタスクが作成されない
- 推定原因: ターミナル環境の問題

## 次のアクション
1. ターミナル環境を再起動
2. Memory MCP ツールの動作確認
3. 必要に応じてチームを再構成
