# paper-reproduction-verifier

论文复现验证 Skill - 独立核验投稿论文的可复现性

## 简介

仅凭作者随稿的代码与数据，验证论文方法与声称结果能否被忠实复现。产出**一份复现报告**（含问题反馈）与复现率判定。

适用于：
- 期刊/会议送审稿件的复现验证
- "复现这篇论文"
- "核对代码与论文是否一致"
- 审稿复现

## 目录结构

```
paper-reproduction-verifier/
├── SKILL.md                      # 主入口（路由文档）
├── README.md                     # 本文件
├── principles.md                 # 核心原则
├── workflow.md                   # 完整流程
├── red-line-checks.md            # 红线检查
├── random-data-handling.md       # 随机数据处理
├── output-management.md          # 产物管理
├── report-writing-guide.md       # 报告撰写规范
├── verdict-criteria.md           # 判定标准
└── templates/
    ├── README.md                 # 模板使用说明
    ├── 复现报告模板.tex          # LaTeX 报告模板
    └── 复现报告示例.pdf          # 示例 PDF
```

## 特性

✅ **产物隔离**：所有复现产物放在作者文件夹外部，不污染原始代码  
✅ **算力评估**：自动评估是否适合本地运行或需转移服务器  
✅ **阶段化执行**：9 个阶段，支持暂停/恢复/跳过  
✅ **红线检查**：自动检测阻断性问题（数据合成、模型未训练、结果硬编码等）  
✅ **精简报告**：面向非技术编辑部人员，聚焦阻断性问题  
✅ **模块化文档**：路由文档结构，易于维护和扩展

## 快速开始

```
/paper-reproduction-verifier
```

Claude 会引导你完成复现验证流程。

## 复现流程

1. **清点文件与缺失项** - 列出随稿文件清单
2. **算力需求预评估** - 判断本地 vs 服务器
3. **论文研读** - 制作声称结果一览表
4. **代码审查与红线检查** - 检测阻断性问题
5. **数据准备** - 下载数据集（自动/手动）
6. **环境搭建** - 安装依赖
7. **执行代码** - 运行并记录日志
8. **实跑vs论文比对** - 逐项对照
9. **产出复现报告** - 生成 PDF 报告

## 判定标准

- **完全复现**（95%-100%）：核心结果差异 <2%，无阻断性问题
- **部分复现**（60%-94%）：核心结果差异 2%-5%，或部分指标无法验证
- **无法复现**（<60%）：核心结果差异 >5%，或存在阻断性问题

## 输出示例

```
论文编号-论文名称/
├── 作者代码文件夹/          (不污染，保持原样)
└── 复现报告/                (所有复现产物)
    ├── 复现报告.pdf
    ├── 复现报告.tex
    ├── run_output.log
    ├── results/
    └── 数据集下载说明.md      (如需要)
```

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

Codex 已支持原生 skills 目录，使用与 Claude Code 相同的 `SKILL.md` 约定：

- **macOS / Linux**：
  ```bash
  mkdir -p ~/.codex/skills
  cp -r paper-reproduction-verifier ~/.codex/skills/
  ```
- **Windows**：复制到 `%USERPROFILE%\.codex\skills\paper-reproduction-verifier\`。

### 3) OpenCode

OpenCode 支持自定义命令/agent。配置目录——macOS/Linux：`~/.config/opencode/`；Windows：`%USERPROFILE%\.config\opencode\`；项目级：`.opencode/`。

- **自定义命令**：在 `.opencode/command/repro-verify.md` 写一行引用或直接粘贴 `SKILL.md` 正文。
- **自定义 agent**：在 OpenCode 的 agent 配置中，把 `SKILL.md` 正文作为该 agent 的 system prompt。

---

## 交付物编译

```bash
xelatex 复现报告模板.tex      # 需要 TeX 发行版（MiKTeX/TeX Live）+ ctex 宏包
```

## 依赖

核验时按被审稿件自身的依赖搭环境；本 skill 本身无运行时依赖，仅需一个能跑 xelatex 的环境来编译报告。

## 贡献

欢迎提 Issue 和 PR！

## License

MIT
