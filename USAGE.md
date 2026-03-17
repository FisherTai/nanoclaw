# NanoClaw 使用指南

## 服務管理 (macOS)

```bash
# 啟動
launchctl load ~/Library/LaunchAgents/com.nanoclaw.plist

# 停止
launchctl unload ~/Library/LaunchAgents/com.nanoclaw.plist

# 重啟
launchctl kickstart -k gui/$(id -u)/com.nanoclaw
```

停止服務後 Docker 不需要特別關閉，不會有容器在跑。想省資源可以退出 Docker Desktop。

## 查看日誌

```bash
tail -f logs/nanoclaw.log
```

容器日誌在 `groups/telegram_main/logs/` 下。

## 換模型

編輯 `.env`，修改：

```
CLAUDE_CODE_USE_MODEL=模型ID
```

然後重啟服務：

```bash
launchctl kickstart -k gui/$(id -u)/com.nanoclaw
```

## Claude Code 操作

在專案目錄下開啟 Claude Code 即可使用各種技能：

```bash
cd /Users/fisher/workspace/04.open-source-clone/nanoclaw
claude
```

常用技能：
- `/setup` — 重新設定或新增配置
- `/debug` — 排查問題
- `/customize` — 新增頻道、整合、修改行為
- `/add-telegram` — 新增/重新設定 Telegram
- `/update-nanoclaw` — 從上游拉取更新

## 目前配置

| 項目 | 值 |
|------|-----|
| Fork | `FisherTai/nanoclaw` |
| 上游 | `qwibitai/nanoclaw` |
| 容器運行時 | Docker Desktop |
| API 提供者 | NanoGPT.com (`ANTHROPIC_BASE_URL`) |
| 頻道 | Telegram (`tg:997999922`，主聊天) |
| 服務 | launchd (`com.nanoclaw.plist`) |

## 清除對話紀錄

```bash
# 清除 Telegram 主聊天的 session（下次對話會從新 session 開始）
sqlite3 store/messages.db "DELETE FROM sessions WHERE group_folder = 'telegram_main'"

# 重啟服務
launchctl kickstart -k gui/$(id -u)/com.nanoclaw
```

## 拉取上游更新

```bash
claude
# 然後輸入 /update-nanoclaw
```

或手動：

```bash
git fetch upstream main
git merge upstream/main
npm install && npm run build
launchctl kickstart -k gui/$(id -u)/com.nanoclaw
```
