# 如何在 Claude Code 中使用中转 API

本指南介绍如何将自定义中转 API（Relay / Proxy API）配置到 Claude Code 中，使 Claude Code 通过你的中转地址发送请求。

---

## 前提条件

- 已安装 [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview)（`npm install -g @anthropic-ai/claude-code`）
- 拥有一个兼容 Anthropic API 格式的中转 API 地址（例如 `https://your-relay.example.com`）和对应的 API Key

---

## 方法一：环境变量（推荐）

在启动 Claude Code 之前，设置以下两个环境变量：

| 环境变量 | 说明 |
|---|---|
| `ANTHROPIC_BASE_URL` | 中转 API 的根地址（不含 `/v1` 路径） |
| `ANTHROPIC_API_KEY` | 中转服务提供的 API Key |

### Linux / macOS

**临时生效（仅当前终端会话）：**

```bash
export ANTHROPIC_BASE_URL="https://your-relay.example.com"
export ANTHROPIC_API_KEY="sk-your-relay-api-key"
claude
```

**永久生效（写入 Shell 配置文件）：**

```bash
# 以 ~/.bashrc 为例，zsh 用户请改为 ~/.zshrc
echo 'export ANTHROPIC_BASE_URL="https://your-relay.example.com"' >> ~/.bashrc
echo 'export ANTHROPIC_API_KEY="sk-your-relay-api-key"'          >> ~/.bashrc
source ~/.bashrc
```

### Windows（PowerShell）

**临时生效（仅当前会话）：**

```powershell
$env:ANTHROPIC_BASE_URL = "https://your-relay.example.com"
$env:ANTHROPIC_API_KEY  = "sk-your-relay-api-key"
claude
```

**永久生效（写入系统环境变量）：**

```powershell
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL", "https://your-relay.example.com", "User")
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY",  "sk-your-relay-api-key",          "User")
```

---

## 方法二：`claude config` 命令

Claude Code 提供了内置配置命令，可将中转地址持久化保存到本地配置文件中（无需每次设置环境变量）：

```bash
# 设置中转 API 地址
claude config set apiBaseUrl "https://your-relay.example.com"

# 设置 API Key（可选，也可以继续通过环境变量传入）
claude config set apiKey "sk-your-relay-api-key"
```

查看当前配置：

```bash
claude config get apiBaseUrl
```

恢复默认（使用官方 Anthropic API）：

```bash
claude config set apiBaseUrl "https://api.anthropic.com"
# 如果之前通过 config 设置了中转 Key，也需要一并更换为官方 API Key
claude config set apiKey "sk-ant-your-official-api-key"
```

---

## 验证配置是否生效

启动 Claude Code 后，可以使用 `/status` 命令查看当前连接信息，确认 API 地址已指向你的中转服务：

```
> /status
```

也可以发送一条简单的消息测试连通性：

```
> Hello, are you working?
```

如果收到正常回复，则说明中转 API 配置成功。

---

## 常见问题

### Q：提示 `401 Unauthorized`
- 检查 `ANTHROPIC_API_KEY` 是否为中转服务的 Key，而非 Anthropic 官方 Key（两者通常不通用）。

### Q：提示 `ECONNREFUSED` 或连接超时
- 确认 `ANTHROPIC_BASE_URL` 地址正确且可访问（可在浏览器或 `curl` 中测试）。
- 检查防火墙或代理设置是否阻断了该地址。

### Q：中转地址需要带 `/v1` 吗？
- **不需要**。只需填写根地址，例如 `https://your-relay.example.com`，Claude Code 会自动拼接 `/v1/messages` 等路径。

### Q：如何在项目级别单独设置，而不影响全局配置？
- 在项目根目录执行 `claude config set --project apiBaseUrl "..."` 即可为该项目单独配置，不影响全局设置。

---

## 参考链接

- [Claude Code 官方文档](https://docs.anthropic.com/en/docs/claude-code/overview)
- [Anthropic API 参考](https://docs.anthropic.com/en/api/getting-started)