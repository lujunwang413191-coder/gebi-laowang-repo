# OpenClaw 配置参考

## agents.list 结构

```json
{
  "id": "string",          // 唯一标识，英文
  "name": "string",        // 显示名
  "workspace": "string",   // 工作区路径
  "agentDir": "string",    // agent状态目录
  "default": true          // 仅主agent设置
}
```

## channels.feishu 结构

```json
{
  "enabled": true,
  "defaultAccount": "default",
  "domain": "feishu",
  "groupPolicy": "open",
  "accounts": {
    "default": {
      "appId": "cli_xxx",
      "appSecret": "xxx"
    },
    "{accountName}": {
      "appId": "cli_xxx",
      "appSecret": "xxx"
    }
  }
}
```

## bindings 结构

```json
{
  "agentId": "string",     // agents.list中的id
  "match": {
    "channel": "feishu",
    "accountId": "string"  // channels.feishu.accounts中的key
  }
}
```

## 分流规则实现

### 规则①：按账号分流（已实现）
- 每个channel账号绑定到独立agent
- 用户接触不同bot账号 = 接触不同agent

### 规则②：关键词触发（需主agent判断）
- 主agent（main）接收所有消息
- 分析消息内容，含特定关键词 → 转发到对应agent
- 用 `sessions_send(sessionKey, message)` 转发

### 规则③：主agent判断转发
- 主agent理解意图后主动分发
- 使用 `sessions_spawn` 拉起子agent处理

## 验证命令

```bash
# JSON格式验证
cat ~/.openclaw/openclaw.json | python3 -m json.tool > /dev/null

# 查看agent列表
openclaw agents list

# 查看bindings
openclaw agents bindings

# 查看网关状态
openclaw gateway status

# 查看日志
tail -20 /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log
```

## 常见错误

- `bindings.0: Invalid input` → 去掉description字段，只保留agentId和match
- `accountId not found` → 检查channels.feishu.accounts中是否有对应key
- `workspace not found` → 检查路径是否正确创建
