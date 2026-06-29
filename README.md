# paper-reproduction-verifier

一个可移植的 **Agent Skill**：独立核验投稿论文的可复现性——仅凭作者随稿的代码与数据，
验证论文方法与声称结果能否被忠实复现，产出**一份复现报告**（含问题反馈）与复现率判定。

## 目录结构

```
paper-reproduction-verifier/
├── SKILL.md                      # 技能本体（指令 + 流程 + 红线检查 + 随机数据处理）
├── README.md                     # 本文件：说明与各 Agent 安装方法
└── templates/
    ├── 复现报告模板.tex          # 交付物 LaTeX 模板（xelatex + ctex 编译）
    └── 复现报告示例.pdf          # 已生成的成品示例（匿名占位，仅示意结构）
```

> 想先看产出长什么样，直接打开 `templates/复现报告示例.pdf`。该示例内容全为虚构占位，
> 仅演示报告结构与"每个位置该写什么"，不含任何真实稿件信息。

## 核心能力

- 分阶段流程：清点 → 论文研读 → 代码审查 → 复现执行 → 比对 → 产出。
- **红线检查**：核心模型是否真训练/数据是否合成/指标是否真计算/结果是否硬编码。
- **随机数据处理**：多种子统计取值范围，区分"随机抽样差异"与"结构性不可能"，避免误导作者。
- 单一交付物：复现报告（问题反馈合并为一节）+ 复现率。

---

## 安装到各 Agent

从 GitHub 取得后（`git clone` 或下载 zip），按你的 Agent 选对应方式安装。

### 1) Claude Code（个人级 / 项目级）

- **macOS / Linux**（个人级）：
  ```bash
  mkdir -p ~/.claude/skills
  cp -r paper-reproduction-verifier ~/.claude/skills/
  ```
  克隆后一步到位：
  ```bash
  git clone <repo-url> ~/.claude/skills/paper-reproduction-verifier
  ```
- **Windows**（个人级）：复制到 `%USERPROFILE%\.claude\skills\paper-reproduction-verifier\`。
- **项目级**（任意系统）：放到仓库的 `.claude/skills/paper-reproduction-verifier/`。

之后用 `/paper-reproduction-verifier` 触发，或直接说"复现核验这篇论文"由 description 自动匹配。

### 2) OpenAI Codex CLI

Codex 已支持原生 skills 目录，使用与 Claude Code 相同的 `SKILL.md` 约定（`name` + `description` frontmatter），
本 skill 可直接放入：

- **macOS / Linux**：
  ```bash
  mkdir -p ~/.codex/skills
  cp -r paper-reproduction-verifier ~/.codex/skills/
  # 或： git clone <repo-url> ~/.codex/skills/paper-reproduction-verifier
  ```
- **Windows**：复制到 `%USERPROFILE%\.codex\skills\paper-reproduction-verifier\`。

放好后 Codex 会按 `description` 自动匹配触发。
（备选：也可把 `SKILL.md` 正文并入 `AGENTS.md` 作为常驻指令，但有原生 skills 目录时无需如此。）

### 3) OpenCode

OpenCode 支持自定义命令/agent。配置目录——macOS/Linux：`~/.config/opencode/`；Windows：`%USERPROFILE%\.config\opencode\`；项目级：`.opencode/`。

- **自定义命令**：在 `.opencode/command/repro-verify.md` 写一行引用或直接粘贴 `SKILL.md` 正文，
  用 `/repro-verify` 触发。
- **自定义 agent**：在 OpenCode 的 agent 配置中，把 `SKILL.md` 正文作为该 agent 的 system prompt。

> 通用做法：任何支持"自定义 system prompt / 指令文件"的 Agent，都可直接把 `SKILL.md` 正文作为指令注入；
> `templates/复现报告模板.tex` 作为交付物模板随附即可。

---

## 交付物编译

```bash
xelatex 复现报告模板.tex      # 需要 TeX 发行版（MiKTeX/TeX Live）+ ctex 宏包，支持中文
```

## 依赖（被核验项目通常需要）

核验时按被审稿件自身的依赖搭环境；本 skill 本身无运行时依赖，仅需一个能跑 xelatex 的环境来编译报告。
