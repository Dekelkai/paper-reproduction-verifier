# 复现产物管理

## 核心原则

**所有复现产物放在作者文件夹外部**，避免污染原始代码。

## 目录结构

```
论文编号-论文名称/
├── 作者代码文件夹/          (不污染，保持原样)
│   ├── main.py
│   ├── requirements.txt
│   ├── README.md
│   └── dataset/             (数据集可在此，但运行产物禁止)
│
└── 复现报告/                (新建，所有复现产物)
    ├── 复现报告.tex
    ├── 复现报告.pdf
    ├── run_output.log       (完整运行日志)
    ├── results/             (从作者文件夹复制)
    │   ├── metrics/         (CSV指标文件)
    │   └── plots/           (可视化图表)
    ├── 数据集下载说明.md     (如需要)
    └── Skill修改需求记录.md  (可选)
```

## 操作流程

### 1. 运行前：清理作者文件夹
```python
# 删除作者文件夹内的临时产物
import shutil
dirs_to_clean = ["results", "outputs", "figures", "plots", "logs", "feat_cache"]
for d in dirs_to_clean:
    if (author_dir / d).exists():
        shutil.rmtree(author_dir / d)
```

### 2. 运行中：指定输出到外部目录
```python
# 示例：如果代码允许配置输出路径
REPORT_DIR = author_dir.parent / "复现报告"
REPORT_DIR.mkdir(parents=True, exist_ok=True)

# 重定向输出
results_dir = REPORT_DIR / "results"
logs_dir = REPORT_DIR / "logs"
```

**注意**：如果代码不支持配置输出路径，则先在作者文件夹内生成，运行后立即复制到外部。

### 3. 运行后：复制结果到外部目录
```python
# 复制结果到复现报告目录
import shutil

src = author_dir / "results"
dst = REPORT_DIR / "results"
if src.exists():
    shutil.copytree(src, dst, dirs_exist_ok=True)

# 复制日志
shutil.copy(author_dir / "run.log", REPORT_DIR / "run_output.log")
```

### 4. 清理作者文件夹（可选）
```python
# 运行后再次清理，恢复原始状态
for d in dirs_to_clean:
    if (author_dir / d).exists():
        shutil.rmtree(author_dir / d)
```

## 禁止污染的文件/目录

**绝对禁止**在作者文件夹内留下：
- `results/`, `outputs/`, `figures/`, `plots/`
- `logs/`, `run.log`
- `*.pdf`, `*.tex` (复现报告)
- `*.pyc`, `__pycache__/`
- `.ipynb_checkpoints/`
- 任何非作者原始提供的文件

**允许**在作者文件夹内：
- `dataset/` (如果代码要求数据集在此)
- `.env` (如果需要配置运行参数)
- 作者提供的模型权重文件（如果作者随稿提供）

## 特殊情况处理

### 情况1：代码硬编码输出路径
如果代码中写死了 `results/` 路径，无法配置，则：
1. 允许代码在作者文件夹内生成
2. 运行完成后立即复制到外部
3. 复制完成后删除作者文件夹内的临时产物

### 情况2：数据集下载到作者文件夹
如果代码自动下载数据集到 `dataset/`，则允许保留（数据集属于作者代码的输入，不算污染）。

但如果代码同时在 `dataset/` 内生成处理后的中间文件（如 `dataset/processed/`），则需在报告中说明，并考虑复制到外部。
