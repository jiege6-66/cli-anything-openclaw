<h1 align="center"><img src="assets/icon.png" alt="" width="64" style="vertical-align: middle;">&nbsp; CLI-Anything for OpenClaw</h1>

<p align="center">
  <strong><a href="https://github.com/HKUDS/CLI-Anything">HKUDS/CLI-Anything</a> 的 OpenClaw Skills 适配版</strong><br>
  通过自动化 CLI 生成，让任意软件变成 Agent 可调用的工具
</p>

<p align="center">
  <a href="#-openclaw-快速开始"><img src="https://img.shields.io/badge/OpenClaw_Skills-5_个-blue?style=for-the-badge" alt="Skills"></a>
  <a href="#-上游实测展示"><img src="https://img.shields.io/badge/Demo-11_款软件-green?style=for-the-badge" alt="Demos"></a>
  <a href="#-测试结果"><img src="https://img.shields.io/badge/测试-1%2C508_通过-brightgreen?style=for-the-badge" alt="Tests"></a>
  <a href="#-许可证"><img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="License"></a>
</p>

---

## ⚡ 这是什么？

这是 [CLI-Anything](https://github.com/HKUDS/CLI-Anything) 项目的 **OpenClaw skills 适配版**。

它把上游面向 Claude Code / OpenCode 的命令体系，改造成 **OpenClaw 可直接加载的 AgentSkills**，让 OpenClaw agent 可以用标准工具（`read`、`exec`、`write`、`edit`）来完成：

- 构建完整 CLI harness
- 扩展已有 harness 覆盖范围
- 运行测试并更新 TEST.md
- 按 HARNESS.md 进行标准验证
- 列出当前机器上的 CLI-Anything 工具

> **上游仓库**：<https://github.com/HKUDS/CLI-Anything>
>
> 原项目提供 Claude Code 插件和 OpenCode 命令；本仓库把它整理成 OpenClaw-first 的 skills 结构，同时保留原有方法论文档、参考命令和相关资源。

---

## 🚀 OpenClaw 快速开始

### 安装方式

把本仓库里的 `skills/` 安装到 OpenClaw 的技能目录即可。

#### 方式 1：安装到当前 workspace

```bash
ln -s /path/to/cli-anything-openclaw/skills/* ~/.openclaw/workspace/skills/
```

#### 方式 2：安装到全局共享 skills

```bash
ln -s /path/to/cli-anything-openclaw/skills/* ~/.openclaw/skills/
```

#### 方式 3：直接克隆后链接

```bash
cd ~/.openclaw/skills
git clone https://github.com/jiege6-66/cli-anything-openclaw.git _cli-anything-repo
for d in _cli-anything-repo/skills/*/; do ln -s "$(realpath "$d")" .; done
```

安装后，OpenClaw 新会话会根据 skill 的 `description` 自动发现这些技能。

### 使用方式

安装完后，直接自然语言告诉 OpenClaw 你要做什么即可：

- “从 `~/src/gimp` 给 GIMP 生成一个 CLI” → 使用 `cli-anything`
- “给 Blender 的 harness 增加粒子系统相关命令” → 使用 `cli-anything-refine`
- “跑一下 inkscape harness 的测试” → 使用 `cli-anything-test`
- “按标准验证 audacity 的 harness” → 使用 `cli-anything-validate`
- “列出当前机器上的 CLI-Anything 工具” → 使用 `cli-anything-list`

---

## 📦 Skills 总览

对 OpenClaw 用户来说，**`skills/` 目录就是主入口**。

| Skill | 作用 | 对应上游命令 |
|------|------|-------------|
| [`cli-anything`](skills/cli-anything/SKILL.md) | 构建完整 CLI harness（7 阶段） | `/cli-anything` |
| [`cli-anything-refine`](skills/cli-anything-refine/SKILL.md) | 扩展已有 harness 的覆盖范围 | `/cli-anything-refine` |
| [`cli-anything-test`](skills/cli-anything-test/SKILL.md) | 运行测试并更新 TEST.md | `/cli-anything-test` |
| [`cli-anything-validate`](skills/cli-anything-validate/SKILL.md) | 对照 HARNESS.md 做标准验证 | `/cli-anything-validate` |
| [`cli-anything-list`](skills/cli-anything-list/SKILL.md) | 列出已安装 / 已生成的 CLI 工具 | `/cli-anything-list` |

### 与上游版的关键区别

| 方面 | 上游（Claude Code / OpenCode） | 本仓库（OpenClaw） |
|------|-------------------------------|--------------------|
| 交互方式 | 斜杠命令 | OpenClaw skills + `read/exec/write/edit` |
| 发现方式 | 插件市场 / 手动复制 | OpenClaw 自动加载 skill |
| 方法论来源 | `HARNESS.md` | 同一个 `HARNESS.md`，只做引用不重复拷贝 |
| 运行环境 | Claude Code / OpenCode 会话 | 任意 OpenClaw agent 会话 |

---

## 📖 方法论核心：HARNESS.md

CLI-Anything 的**唯一权威方法论文档**在这里：

📄 **[`cli-anything-plugin/HARNESS.md`](cli-anything-plugin/HARNESS.md)**

它定义了：

- 7 阶段流程：分析 → 设计 → 实现 → 规划测试 → 编写测试 → 记录测试 → 发布
- 目录结构与实现模式
- 渲染鸿沟、滤镜翻译、时间码精度、输出验证等关键坑点
- 测试策略、规则和质量标准

本仓库中的所有 skill 都基于它来工作。**使用 skill 前，先读 HARNESS.md。**

---

## 🎯 上游实测展示

上游项目已经给 11 款软件做了实测示例：

| 软件 | 领域 | CLI 命令 | 测试数 |
|------|------|---------|-------|
| 🎨 GIMP | 图像编辑 | `cli-anything-gimp` | ✅ 107 |
| 🧊 Blender | 3D 建模 | `cli-anything-blender` | ✅ 208 |
| ✏️ Inkscape | 矢量图形 | `cli-anything-inkscape` | ✅ 202 |
| 🎵 Audacity | 音频制作 | `cli-anything-audacity` | ✅ 161 |
| 📄 LibreOffice | 办公套件 | `cli-anything-libreoffice` | ✅ 158 |
| 📹 OBS Studio | 直播录制 | `cli-anything-obs-studio` | ✅ 153 |
| 🎞️ Kdenlive | 视频剪辑 | `cli-anything-kdenlive` | ✅ 155 |
| 🎬 Shotcut | 视频剪辑 | `cli-anything-shotcut` | ✅ 154 |
| 📞 Zoom | 视频会议 | `cli-anything-zoom` | ✅ 22 |
| 📐 Draw.io | 图表绘制 | `cli-anything-drawio` | ✅ 138 |
| ✨ AnyGen | AI 内容生成 | `cli-anything-anygen` | ✅ 50 |

> 总计 **1,508 项测试**，通过率 **100%**。

---

## 📊 测试结果

每个 CLI harness 都按多层方式验证：

| 层级 | 验证内容 | 示例 |
|------|---------|------|
| **单元测试** | 核心函数隔离验证 | `test_core.py` |
| **E2E（原生文件）** | 工程文件生成链路 | ODF ZIP、MLT XML、SVG |
| **E2E（真实后端）** | 真软件调用 + 真输出 | LibreOffice → PDF，Blender → PNG |
| **CLI 子进程测试** | 已安装命令的真实调用 | `cli-anything-gimp --json project new` |

```text
================================ Test Summary ================================
gimp          107 passed  ✅   (64 unit + 43 e2e)
blender       208 passed  ✅   (150 unit + 58 e2e)
inkscape      202 passed  ✅   (148 unit + 54 e2e)
audacity      161 passed  ✅   (107 unit + 54 e2e)
libreoffice   158 passed  ✅   (89 unit + 69 e2e)
obs-studio    153 passed  ✅   (116 unit + 37 e2e)
kdenlive      155 passed  ✅   (111 unit + 44 e2e)
shotcut       154 passed  ✅   (110 unit + 44 e2e)
zoom           22 passed  ✅   (22 unit + 0 e2e)
drawio        138 passed  ✅   (116 unit + 22 e2e)
anygen         50 passed  ✅   (40 unit + 10 e2e)
──────────────────────────────────────────────────────────────────────────────
TOTAL        1,508 passed  ✅   100% pass rate
```

---

## 📂 仓库结构

```text
cli-anything-openclaw/
│
├── skills/                              ⭐ 从这里开始：OpenClaw skills
│   ├── cli-anything/SKILL.md            构建完整 CLI harness
│   ├── cli-anything-refine/SKILL.md     扩展 harness 覆盖范围
│   ├── cli-anything-test/SKILL.md       运行测试并更新文档
│   ├── cli-anything-validate/SKILL.md   按标准进行验证
│   └── cli-anything-list/SKILL.md       列出可用 CLI 工具
│
├── cli-anything-plugin/                 上游插件与方法论文档
│   ├── HARNESS.md                       ⭐ 方法论 SOP（唯一权威来源）
│   ├── README.md                        插件说明
│   ├── QUICKSTART.md                    快速上手
│   ├── PUBLISHING.md                    发布说明
│   ├── repl_skin.py                     统一 REPL 皮肤
│   └── LICENSE                          MIT License
│
├── opencode-commands/                   上游 OpenCode 命令（参考保留）
├── assets/                              图片资源
├── README.md                            英文说明（OpenClaw 版）
└── README_CN.md                         中文说明（本文件）
```

---

## 🤔 为什么还是 CLI？

CLI 是人类和 Agent 共通的最低摩擦接口：

- **结构化、可组合**：命令天然适合 LLM 解析和串联
- **轻量、跨平台**：没有 GUI 自动化的那堆脆弱依赖
- **可发现**：`--help` 和 `which` 就能暴露能力
- **Agent 友好**：`--json` 输出便于结构化消费
- **行为可预测**：比点按钮、看截图稳定得多

---

## 🏗️ 核心设计原则

1. **调用真实软件**：生成合法工程文件，然后交给真实应用渲染 / 导出
2. **双交互模型**：支持状态化 REPL，也支持子命令脚本模式
3. **统一体验**：通过 `repl_skin.py` 统一 CLI 的交互皮肤
4. **Agent 原生**：命令支持 `--json`，可被 `which` / `--help` 发现
5. **零妥协**：真实软件是硬依赖，不做“玩具版替代实现”

---

## 🤝 贡献方向

欢迎继续补：

- 新的 OpenClaw skills
- 更完整的 OpenClaw 集成说明
- 更多目标软件的 harness
- 对上游 `HARNESS.md` 的经验回流

---

## 📄 许可证

上游 CLI-Anything 项目在 README 和 `cli-anything-plugin/LICENSE` 中都声明为 **MIT License**。

本适配仓库也沿用 MIT，并在仓库根目录补充了顶层 `LICENSE`。

---

## 🔗 链接

- 上游仓库：<https://github.com/HKUDS/CLI-Anything>
- 本适配仓库：<https://github.com/jiege6-66/cli-anything-openclaw>
- OpenClaw：<https://github.com/nicholasgasior/openclaw>
- 方法论文档：[`cli-anything-plugin/HARNESS.md`](cli-anything-plugin/HARNESS.md)

---

<div align="center">

**CLI-Anything for OpenClaw** — *让任意软件变成 Agent 可稳定调用的工具。*

<sub>基于 HKUDS/CLI-Anything 改造 | 5 个 OpenClaw skills | 11 个实测示例 | 1,508 项测试通过</sub>

</div>
