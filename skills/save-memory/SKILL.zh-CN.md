---
name: save-memory
description: 回顾本次对话，提取关键经验并保存到项目的记忆系统中，让下次会话自动加载
---

# Save Memory Skill

你是一个经验提取器。当用户调用此 skill 时，执行以下步骤：

## 步骤

1. **扫描本次对话**，识别以下类型的经验：
   - **踩坑**：排查了什么问题，根因是什么，最终怎么解决的
   - **新知识**：发现了什么之前不知道的规则、限制、优先级
   - **用户偏好**：用户明确表达的习惯、偏好、禁忌
   - **实现方案**：某个功能的正确实现方式（特别是经过多次失败尝试后确定的）

2. **排除不值得保存的内容**：
   - 单行 typo 修复
   - 一眼就能看出来的语法错误
   - 用户只是随口问了一下的东西
   - 代码中已经能明显看出的事实

3. **将每个经验写入独立的 memory 文件**到项目记忆目录。路径规则：
   - 项目记忆目录 = `~/.claude/projects/<project-slug>/memory/`
   - project-slug 是用 `-` 替换路径分隔符的工作目录完整路径（如 `D--Vibecoding-TraeSolo-api-gateway-manager`）
   - 如果目录不存在，先创建它
   - 每个文件格式：

```markdown
---
name: {{short-kebab-case-slug}}
description: {{一行摘要，用于判断未来相关性}}
metadata:
  type: {{user|feedback|project|reference}}
---

{{正文 — feedback/project 类型用"规则"+"Why:"+"How to apply:"结构}}
```

4. **更新项目 MEMORY.md 索引**：在 `~/.claude/projects/<project-slug>/memory/MEMORY.md` 中添加新条目，格式：
   `- [标题](file.md) — 一行摘要`

5. **跨项目经验**：如果某个经验是通用性的（不限定当前项目），也复制到 `~/.claude/memory/MEMORY.md` 用户级记忆，但只记录索引（指向最佳实践或通用规则，不重复项目特定的上下文）。

6. **汇报**：简要告知用户保存了哪些经验。

## 类型选择

- `user`：关于用户的角色、习惯、偏好 → 存到用户级 `~/.claude/memory/`
- `feedback`：用户给出的工作方式指导 → 存到项目级
- `project`：项目架构、Bug、实现方案 → 存到项目级
- `reference`：外部系统链接、文档地址 → 存到项目级

## 注意

- 不要保存能从代码中直接推导出的信息（文件路径、代码模式等）
- 不要为不值得跨会话保留的小事创建记忆
- 如果已有相关记忆，更新而非新建
- 保持 MEMORY.md 在 200 行以内
