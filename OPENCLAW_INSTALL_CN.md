# OpenClaw 安装指南（CLI-Anything Skills 版）

这份文档专门说明：如何把 `cli-anything-openclaw` 这套 skills 安装到 OpenClaw 中，并验证是否可正常使用。

> 仓库地址：<https://github.com/jiege6-66/cli-anything-openclaw>

---

## 1. 这不是“插件安装”，而是 Skills 安装

这个仓库不是给 Claude Code 装的插件，也不是 OpenCode 斜杠命令包。

它现在的形态是 **OpenClaw Skills 仓库**，核心内容在：

```text
skills/
├── cli-anything/
├── cli-anything-refine/
├── cli-anything-test/
├── cli-anything-validate/
└── cli-anything-list/
```

OpenClaw 会读取每个目录下的 `SKILL.md`，并在新会话中把它们作为可用 skills 暴露给 agent。

---

## 2. 安装前提

在安装这些 skills 之前，建议确认：

### 必需条件

- 已安装并可运行 **OpenClaw**
- 你知道自己的 OpenClaw 工作区位置
- 当前用户对 `~/.openclaw/` 有读写权限

### 建议条件

- `git` 已安装
- `python3` 可用
- OpenClaw Gateway 正常运行

你可以先检查：

```bash
openclaw status
openclaw gateway status
```

如果这里只是想安装 skills，本身不要求目标软件立即安装；但等你真正运行 `cli-anything` 去生成某个软件的 harness 时，**目标软件本身通常需要安装在系统里**。

---

## 3. 推荐安装方式

有两种推荐方式：

### 方式 A：装到当前 workspace（推荐，影响范围最小）

适合：只想在当前 agent / 当前工作区里使用。

```bash
mkdir -p ~/.openclaw/workspace/skills
cd ~/.openclaw/workspace/skills
git clone https://github.com/jiege6-66/cli-anything-openclaw.git _cli-anything-repo
for d in _cli-anything-repo/skills/*/; do ln -s "$(realpath "$d")" .; done
```

安装后目录大致会变成：

```text
~/.openclaw/workspace/skills/
├── _cli-anything-repo/
├── cli-anything -> _cli-anything-repo/skills/cli-anything/
├── cli-anything-refine -> _cli-anything-repo/skills/cli-anything-refine/
├── cli-anything-test -> _cli-anything-repo/skills/cli-anything-test/
├── cli-anything-validate -> _cli-anything-repo/skills/cli-anything-validate/
└── cli-anything-list -> _cli-anything-repo/skills/cli-anything-list/
```

---

### 方式 B：装到全局共享 skills

适合：希望这台机器上的多个 OpenClaw agent 都能看到这些 skills。

```bash
mkdir -p ~/.openclaw/skills
cd ~/.openclaw/skills
git clone https://github.com/jiege6-66/cli-anything-openclaw.git _cli-anything-repo
for d in _cli-anything-repo/skills/*/; do ln -s "$(realpath "$d")" .; done
```

---

## 4. 不想用符号链接的话，也可以直接复制

如果你不喜欢 symlink，也可以直接复制：

### 复制到 workspace

```bash
mkdir -p ~/.openclaw/workspace/skills
git clone https://github.com/jiege6-66/cli-anything-openclaw.git /tmp/cli-anything-openclaw
cp -r /tmp/cli-anything-openclaw/skills/* ~/.openclaw/workspace/skills/
```

### 复制到全局

```bash
mkdir -p ~/.openclaw/skills
git clone https://github.com/jiege6-66/cli-anything-openclaw.git /tmp/cli-anything-openclaw
cp -r /tmp/cli-anything-openclaw/skills/* ~/.openclaw/skills/
```

缺点是后续更新没有 symlink 方便。

---

## 5. 安装后如何生效

### 最稳的方法：开一个新会话

OpenClaw 对 skills 是按 session 快照加载的。最稳妥的办法是：

- 新开一个会话
- 或 `/new`
- 或重启 Gateway 后再开新会话

### 如果你想手动刷新

可以尝试：

```bash
openclaw gateway restart
```

但即使 Gateway 刷新了，**当前已存在的旧 session** 也不一定立刻拿到新的 skills 列表。最稳还是新会话。

---

## 6. 如何确认安装成功

### 方法 1：直接看目录

```bash
find ~/.openclaw/workspace/skills -maxdepth 2 -name SKILL.md
find ~/.openclaw/skills -maxdepth 2 -name SKILL.md
```

如果你能看到：

- `cli-anything/SKILL.md`
- `cli-anything-refine/SKILL.md`
- `cli-anything-test/SKILL.md`
- `cli-anything-validate/SKILL.md`
- `cli-anything-list/SKILL.md`

说明文件层面已经装好了。

### 方法 2：在新会话里直接问 agent

你可以在 OpenClaw 新会话里直接说：

- “列出可用的 cli-anything skills”
- “用 cli-anything-list 看看当前机器上的工具”
- “帮我读一下 cli-anything 的 skill”

如果 agent 能正确命中这些 skills，就说明加载成功。

### 方法 3：看 skills 是否出现在系统 prompt 注入列表

如果你在调试环境里能看到 skills 列表，应该会出现类似：

- `cli-anything`
- `cli-anything-refine`
- `cli-anything-test`
- `cli-anything-validate`
- `cli-anything-list`

---

## 7. 这 5 个 skill 分别干什么

### `cli-anything`

用于从软件源码或 GitHub 仓库出发，按 7 阶段方法生成完整 CLI harness。

### `cli-anything-refine`

用于扩展已有 harness 的覆盖范围，比如增加更多命令、更多测试、补更多功能域。

### `cli-anything-test`

用于执行测试，并把结果补进 `TEST.md`。

### `cli-anything-validate`

用于按 `HARNESS.md` 的标准逐项验证目录结构、实现模式、测试和打包质量。

### `cli-anything-list`

用于列出当前机器上已安装 / 已生成的 CLI-Anything 工具。

---

## 8. 使用时的关键认知

### 1）这些 skill 不会直接“生成一切”

它们提供的是 **方法论 + 执行流程**，本质仍然需要 agent：

- 读源码
- 分析后端
- 设计命令结构
- 写 Python 包
- 写测试
- 验证真实输出

### 2）核心权威文档是 `HARNESS.md`

所有 skill 都要求优先参考：

```text
cli-anything-plugin/HARNESS.md
```

它定义了：

- 目录结构
- 真实软件后端调用要求
- 测试分层
- E2E 标准
- 打包方式
- 一些重要坑点（渲染、滤镜映射、时间码精度等）

### 3）最适合这类软件

这套方法最适合：

- 有源码
- 有稳定数据模型
- 有 CLI / 脚本 / headless 后端
- 可验证真实输出

例如：

- LibreOffice
- Blender
- GIMP
- Inkscape
- Draw.io
- Shotcut / Kdenlive

---

## 9. 一个最小使用示例

### 示例：让 OpenClaw 帮你构建一个 GIMP CLI

在 OpenClaw 新会话里说：

```text
帮我用 cli-anything，从 /path/to/gimp 源码开始，为 GIMP 生成一个完整的 CLI harness。
```

更明确一点也可以：

```text
使用 cli-anything skill，先读取 HARNESS.md，然后分析 /path/to/gimp，按 7 阶段流程构建 agent-harness。
```

### 示例：扩展 Blender harness

```text
使用 cli-anything-refine，把 /path/to/blender 的 harness 扩展到粒子系统和物理模拟相关命令。
```

### 示例：跑测试

```text
使用 cli-anything-test，运行 /path/to/inkscape 对应 harness 的测试，并更新 TEST.md。
```

---

## 10. 常见问题

### Q1：我已经把文件放进 skills 目录了，但 agent 没有触发 skill

常见原因：

- 你还在旧 session 里
- skill 放错目录了
- `SKILL.md` 没被正确发现
- description 不够匹配你当前提问方式

建议：

1. 新开会话
2. 确认目录里有 `SKILL.md`
3. 直接明确提到 skill 名称，比如“使用 cli-anything”

---

### Q2：为什么安装了 skill 还是不能真正生成目标软件的 CLI？

因为这类任务除了 skill 之外，还依赖：

- 目标软件源码可读
- Python 环境可用
- 目标后端工具能调用
- 机器上装了相关真实软件

skill 只是“教会 agent 怎么做”，不是替你提前装好所有依赖。

---

### Q3：修改了 `SKILL.md` 为什么当前会话没变化？

因为 OpenClaw session 会对 skills 做快照。通常要：

- 开新会话
- 或重启 Gateway 后开新会话

---

### Q4：应该装到 `~/.openclaw/skills` 还是 `~/.openclaw/workspace/skills`？

建议：

- **只给当前 agent / 当前工作区用** → `~/.openclaw/workspace/skills`
- **希望多个 agent 共用** → `~/.openclaw/skills`

---

## 11. 更新方式

如果你是用 git clone + symlink 安装的，更新最简单：

```bash
cd ~/.openclaw/skills/_cli-anything-repo
git pull
```

或者如果你装在 workspace：

```bash
cd ~/.openclaw/workspace/skills/_cli-anything-repo
git pull
```

然后：

- 重启 Gateway（可选）
- 开新 OpenClaw 会话

---

## 12. 卸载方式

### 如果你用的是 symlink

删除链接和仓库目录即可。

例如 workspace 安装：

```bash
rm -f ~/.openclaw/workspace/skills/cli-anything
rm -f ~/.openclaw/workspace/skills/cli-anything-refine
rm -f ~/.openclaw/workspace/skills/cli-anything-test
rm -f ~/.openclaw/workspace/skills/cli-anything-validate
rm -f ~/.openclaw/workspace/skills/cli-anything-list
rm -rf ~/.openclaw/workspace/skills/_cli-anything-repo
```

### 如果你是复制安装

直接删对应目录即可。

---

## 13. 推荐的安装后自检流程

建议安装完成后按这个顺序自检：

```bash
# 1) 看 skill 文件是否存在
find ~/.openclaw/workspace/skills -maxdepth 2 -name SKILL.md

# 2) 看 OpenClaw 本身是否正常
openclaw status

# 3) 必要时重启 Gateway
openclaw gateway restart
```

然后新开一个 OpenClaw 会话，直接发：

```text
使用 cli-anything-list，帮我看看当前机器上有哪些 CLI-Anything 工具。
```

如果这一步工作正常，说明这套 skills 已经真正接进 OpenClaw 了。

---

## 14. 相关链接

- 本适配仓库：<https://github.com/jiege6-66/cli-anything-openclaw>
- 上游仓库：<https://github.com/HKUDS/CLI-Anything>
- OpenClaw：<https://github.com/nicholasgasior/openclaw>
- 方法论文档：`cli-anything-plugin/HARNESS.md`

---

如果你准备把这份文档放到仓库导航里，建议下一步：

- 在 `README.md` 和 `README_CN.md` 里增加一个链接到 `OPENCLAW_INSTALL_CN.md`
- 或再补一份英文版 `OPENCLAW_INSTALL.md`
