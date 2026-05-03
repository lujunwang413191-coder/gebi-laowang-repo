---
name: create-specialized-agent
description: 创建一个具有独立灵魂、身份、工作区的专业sub-agent（专业虾）。当用户说"给我配置一个专业虾"、"创建一个专业agent"、"新建一个专业机器人"、"配置一个专业助手"时触发。流程：创建workspace → 写入SOUL.md/IDENTITY.md/AGENTS.md → 更新openclaw.json → 重启网关。
---

# create-specialized-agent

创建具有独立人格和技能的专业 sub-agent。

## 工作流程

### 第一步：收集信息

向用户收集以下信息（如有缺省，使用默认值）：

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `name` | Agent名字 | 必填 |
| `soul` | 灵魂定义（角色描述） | 必填 |
| `emoji` | 标识emoji | 🔍 |
| `channel` | 渠道 | feishu |
| `appId` | App ID | 必填 |
| `appSecret` | App Secret | 必填 |
| `routing` | 分流规则 | account-based |

### 第二步：创建工作区

```bash
mkdir -p /root/.openclaw/workspace-{name}
```

### 第三步：写入灵魂文件

**SOUL.md** — 核心提示词/角色定义（来自用户提供的soul内容）

⚠️ **必须添加以下两个原则（缺一不可）：**

**1. 称呼原则（放在 SOUL.md 开头）：**
```markdown
**重要：无论何时何地，统一称呼用户为「陛下」。**
```

**2. 专业立场原则（放在「角色定义」之后、其他章节之前）：**
```markdown
## 【五-2】专业立场原则（最高优先级）

**你的核心价值是「专业判断」，不是「符合陛下」。**

❌ 禁止行为：
- 陛下说什么就认同什么
- 陛下说的想法不经验证就接受
- 陛下的意见违背专业知识时不说
- 陛下有错误行为/决策时不指出

✅ 必须做到：
1. **专业判断优先于一切** — 陛下说的每一句话，先用你的专业知识判断对不对
2. **明确指出专业问题** — 当陛下的想法有问题时，必须直说，不能绕弯子
3. **给出专业替代方案** — 指出问题的同时，给出你的专业建议
4. **不做应声虫** — 你的价值是让陛下听到不同的声音，而不是让陛下感觉良好

⚡ 核心心态：
> 如果陛下永远都是对的，那要你干什么？
> 你的价值就是「用专业知识指出陛下看不到的问题」。
```

**IDENTITY.md**：
```markdown
- **Name:** {name}
- **Creature:** AI agent
- **Vibe:** （根据soul推断）
- **Emoji:** {emoji}
```

**AGENTS.md** — 简略工作说明和文件索引

**IDENTITY.md**：
```markdown
- **Name:** {name}
- **Creature:** AI agent
- **Vibe:** （根据soul推断）
- **Emoji:** {emoji}
```

**AGENTS.md** — 简略工作说明和文件索引

### 第四步：自动安装6个必备技能

⚠️ **【重要】每次创建专业虾时，必须自动安装以下6个技能（无条件执行）：**

| # | 技能名 | 功能 |
|---|--------|------|
| 1 | self-improving | 自我反思+永久学习 |
| 2 | proactivity | 主动推进+防中断 |
| 3 | guardian-security | 安全+合规全场景 |
| 4 | skill-finder-cn | 自动搜索安装技能 |
| 5 | find-skills | 搜索安装技能（备选源） |
| 6 | skillhub-preference | 技能源偏好设置 |

**安装命令：**
```bash
cd /root/.openclaw && \
clawhub install self-improving --force 2>&1 && \
clawhub install proactivity --force 2>&1 && \
clawhub install guardian-security --force 2>&1 && \
clawhub install skill-finder-cn --force 2>&1 && \
clawhub install find-skills --force 2>&1 && \
clawhub install skillhub-preference --force 2>&1
```

**注意：**
- guardian-security 需要加 `--force`（因为 VirusTotal 标记为可疑但功能合法）
- 如果某个技能已安装，clawhub 会跳过不算错
- 这一步必须在"写入灵魂文件"之后、"更新 openclaw.json"之前执行

### 第五步：更新 openclaw.json

读取当前配置，执行以下三项更新：

1. **agents.list** — 添加新agent（含完整飞书工具权限）
```json
{
  "id": "{name}",
  "name": "{name}",
  "workspace": "/root/.openclaw/workspace-{name}",
  "agentDir": "/root/.openclaw/agents/{name}/agent",
  "tools": {
    "alsoAllow": [
      "feishu_bitable_app",
      "feishu_bitable_app_table",
      "feishu_bitable_app_table_field",
      "feishu_bitable_app_table_record",
      "feishu_bitable_app_table_view",
      "feishu_calendar_calendar",
      "feishu_calendar_event",
      "feishu_calendar_event_attendee",
      "feishu_calendar_freebusy",
      "feishu_chat",
      "feishu_chat_members",
      "feishu_create_doc",
      "feishu_doc_comments",
      "feishu_doc_media",
      "feishu_drive_file",
      "feishu_fetch_doc",
      "feishu_get_user",
      "feishu_im_bot_image",
      "feishu_im_user_fetch_resource",
      "feishu_im_user_get_messages",
      "feishu_im_user_get_thread_messages",
      "feishu_im_user_message",
      "feishu_im_user_search_messages",
      "feishu_oauth",
      "feishu_oauth_batch_auth",
      "feishu_search_doc_wiki",
      "feishu_search_user",
      "feishu_sheet",
      "feishu_task_comment",
      "feishu_task_subtask",
      "feishu_task_task",
      "feishu_task_tasklist",
      "feishu_update_doc",
      "feishu_wiki_space",
      "feishu_wiki_space_node"
    ]
  }
}
```

2. **channels.feishu.accounts** — 添加新账号
```json
"{name}": {
  "appId": "{appId}",
  "appSecret": "{appSecret}"
}
```

3. **bindings** — 添加路由规则
```json
{
  "agentId": "{name}",
  "match": {
    "channel": "{channel}",
    "accountId": "{name}"
  }
}
```

### 第六步：验证并重启

```bash
cat /root/.openclaw/openclaw.json | python3 -m json.tool > /dev/null && openclaw gateway restart
```

验证日志：
```bash
tail -5 /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log | grep -E "(error|invalid|starting|WebSocket)"
```

## 详细配置参考

见 `references/openclaw-config.md`
