# 红线检查

在代码审查阶段，**最先执行**红线检查，最易暴露致命问题。

## 检查项

### 1. 核心模型/方法是否真训练并评测？
还是只初始化搭壳、从未产出论文指标？

**检查方法**：
- 搜索 `train`, `loss`, `fit`, `eval`, 论文中的指标名
- 查看是否真实调用训练函数（如 `model.fit()`, `TabularPredictor.fit()`）
- 验证训练循环是否真实执行

**示例**：
```python
# ✅ 真实训练
predictor = TabularPredictor(label="label").fit(train_data)

# ❌ 只初始化，未训练
model = XGBClassifier()
# 后续无 model.fit() 调用
```

### 2. 数据是真实的还是随机合成的？
合成数据无法支撑实证结论。

**检查方法**：
- 搜索 `np.random`, `random.seed`, `fake`, `synthetic`
- 查看数据加载方式（`read_csv` vs `np.random.randn`）
- 区分：训练用数据 vs 鲁棒性测试用数据

**示例**：
```python
# ✅ 真实数据
df = pd.read_csv("features_3_sec.csv")

# ❌ 合成数据（作为主数据）
X = np.random.randn(1000, 50)
y = np.random.randint(0, 10, 1000)
```

**注意**：如果数据是随机生成的，详见 [random-data-handling.md](random-data-handling.md)

### 3. 论文每个指标代码是否真计算并输出？
无对应计算 = 不可追溯。

**检查方法**：
- 搜索论文中的所有指标名（accuracy, precision, recall, f1, roc_auc 等）
- 验证是否调用 `sklearn.metrics` 或等价计算函数
- 检查结果是否保存到文件（CSV/JSON）

**示例**：
```python
# ✅ 指标真实计算
accuracy = accuracy_score(y_test, y_pred)
roc_auc = roc_auc_score(y_test, y_prob, multi_class="ovr")
```

### 4. 结果是代码产出还是硬编码？
验证随稿数据是否就是代码输出。

**检查方法**：
- 搜索论文关键数值（如 `0.9459`, `94.59`, `0.9978`）
- 检查代码中是否硬编码这些数值
- 如果随稿包含结果文件（如 `results.csv`），验证是否可由代码重新生成

**示例**：
```python
# ❌ 硬编码
accuracy = 0.9459  # 直接写死论文数值

# ✅ 代码产出
accuracy = accuracy_score(y_test, y_pred)
```

## 判定

任一红线检查**命中**，即显著标注，基本可判定为**阻断性问题**。

**阻断性问题示例**：
- 核心模型从未训练，无法产出论文指标
- 数据完全合成，无法支撑实证结论
- 论文指标无对应计算代码，不可追溯
- 结果硬编码，无法验证可复现性
