# Save Memory

Claude Code 的记忆系统 Skill，将对话中的关键经验持久化到项目记忆库，让未来的会话自动加载过去的经验。

## Skills

### save-memory

自动从对话中提取值得保存的知识（踩坑记录、用户偏好、架构决策等），并按项目持久化。

**支持的语言版本：**
- [SKILL.md](skills/save-memory/SKILL.md) - English
- [SKILL.zh-CN.md](skills/save-memory/SKILL.zh-CN.md) - 简体中文

**功能：**
- 自动识别值得保存的经验（无需用户命令）
- 分类存储：user / feedback / project / reference
- 自动更新 MEMORY.md 索引
- 跨项目知识同步

## 安装

将 `skills/save-memory` 目录复制到 `~/.claude/skills/` 即可：

```powershell
# Windows
Copy-Item -Recurse skills\save-memory $env:USERPROFILE\.claude\skills\

# macOS / Linux
cp -r skills/save-memory ~/.claude/skills/
```

## 工作原理

每个项目有一个记忆目录：`~/.claude/projects/<project-slug>/memory/`

```
memory/
├── MEMORY.md          # 索引（每个会话自动加载）
└── <topic>.md        # 各类经验文件
```

用户级别的跨项目记忆：`~/.claude/memory/`