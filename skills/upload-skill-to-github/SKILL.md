---
name: upload-skill-to-github
description: 将本地技能（SKILL.md）打包上传到指定 GitHub 仓库。当用户说"把 XX 技能上传到 GitHub"、"打包上传技能"时触发。
---

# upload-skill-to-github

将本地技能目录打包上传到用户的 GitHub 仓库。

## 工作流程

### 第一步：收集信息

向用户收集：
| 参数 | 说明 | 默认值 |
|------|------|--------|
| `repo` | GitHub 仓库（格式：`user/repo`） | 必填 |
| `skillPath` | 本地技能目录路径 | 必填 |
| `targetDir` | 远程仓库内的目标目录 | `skills/` |

### 第二步：克隆仓库

```bash
gh repo clone {owner}/{repo} /tmp/{repo-name}
```

### 第三步：复制技能文件

```bash
cp -r {skillPath} /tmp/{repo-name}/{targetDir}/
```

### 第四步：提交并推送

```bash
cd /tmp/{repo-name}
git add {targetDir}/$(basename {skillPath})/
git commit -m "feat: 添加 $(basename {skillPath}) 技能"
git push
```

## 认证方式

使用 `gh` CLI 已配置的认证（`gh auth status`），无需额外提供 Token。

## 注意事项

- 如果仓库不存在，提示用户先在 GitHub 创建仓库
- 提交信息自动生成，格式为 `feat: 添加 {技能名} 技能`
- 技能目录结构原样保留

## 示例

用户说："把 create-specialized-agent 技能上传到我的 GitHub"

回复：
```
✅ 正在上传 create-specialized-agent 到 GitHub...
→ 目标仓库：lujunwang413191-coder/gebi-laowang-repo
→ 上传完成！https://github.com/lujunwang413191-coder/gebi-laowang-repo
```
