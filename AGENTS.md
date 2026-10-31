# 项目记忆

## 仓库与远端

- 当前目录是实际运行的小红书 MCP Go 服务；上一级目录还保留早期 Node.js 实验代码和部署笔记。处理服务问题时以本仓库的源码、`README.md` 和 `CLAUDE.md` 为准。
- `origin` 是用户的 fork：`xitu310/xiaohongshu-mcp`；`upstream` 是原仓库：`xpzouying/xiaohongshu-mcp`。`main` 跟踪 `origin/main`。同步前分别 fetch 并检查提交差异，按用户指定的远端合并，不要把 `origin` 当成原仓库。
- 遵守 `CLAUDE.md` 的分支、格式化、测试和远端 review 约定；未经用户明确要求，不提交或推送。

## 本机服务

- 本机由 `launchd` 的 `com.xpzouying.xiaohongshu-mcp` 管理服务，启用了 `KeepAlive`。程序是本目录的 `xhs-mcp`，工作目录为本目录，监听 `127.0.0.1:18060`，MCP 地址为 `http://127.0.0.1:18060/mcp`。
- 更新 Go 源码后必须重新编译二进制，再通过 `launchctl kickstart -k gui/$(id -u)/com.xpzouying.xiaohongshu-mcp` 重启。不要再手动启动第二个同端口实例；先用 `lsof -nP -iTCP:18060 -sTCP:LISTEN` 和 `launchctl list` 核对进程。
- 标准输出和错误日志分别在 `/tmp/xhs-mcp.log`、`/tmp/xhs-mcp.err`。排查超时先看工具名、时间和堆栈，再用只读 MCP 调用复现。`/health` 的 `account` 字段不是登录昵称；登录状态用 `check_login_status` 工具核对。
- `cookies.json` 含登录凭据，不读取其内容、不打印、不提交。服务只绑定本机地址。

## MCP 操作与验证

- 优先通过 MCP 接口操作小红书。`publish_content` 会直接发布，并要求至少一张图片；不要把它当作“保存草稿”。每次升级后先检查实际 `tools/list`，不要假定工具清单不变。
- 修改 Go 源码后格式化受影响文件并运行 `go test ./...`。发布新二进制后检查健康接口、`tools/list` 和一个只读工具；不为了测试而发布真实内容。
