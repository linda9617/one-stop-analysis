# One-Stop Analysis — Update Notes

---

## 2026-05-18

### 用户手册
- 新建 `one-stop-analysis-guide.html`：独立 HTML 用户手册，涵盖所有 4 个步骤的每个选项说明、适用场景及完整统计词汇表
- 词汇表包含：FC / Z-score / SSMD / Z' factor / S_pooled / D-ratio / Welch's t-test / 2-way ANOVA / Holm / BH / Grubbs / IQR / ROUT / EC50 / 4PL
- 每个 mean/median 变体公式并排展示，median 变体标注 **Robust** 绿色标签
- D-ratio 条目重写：明确公式含义（MDD / Signal window）、1.4826 的来源、以及 D-ratio 与 hit threshold 的对应关系表

### Guide 入口
- 主工具 `Statistics Table` 标题右侧新增 **Guide** 小标签，点击跳转至 `#assay-window`
- `Select Table & Graph Type` 标题右侧新增 **Guide** 小标签，点击跳转至 `#step4`
- 右下角新增固定 **?** 浮动按钮，点击在新标签页打开完整用户手册

### Bug fix — Column chart Row factor
- 修复：选择 Row factor 列时会错误将该列的 Group by checkbox 变灰的问题
- 现在 Row factor 不再传入 `_syncGroupByExclusions`，仅 Y value 列会触发对应 checkbox 置灰

### Prism Export — Column chart：Welch's t-test 星号标注
- 新增 **Welch's t-test** checkbox，勾选后在预览图上标注显著性星号
- **Comparison**：`vs Control`（每组 vs 指定 control 组）或 `All pairs`（所有两两组合）
- **Control group**：动态下拉，自动填充当前图的分组列表
- **Correction**：`None`（无校正）/ `Holm (FWER)`（家族错误率控制）/ `BH (FDR)`（假发现率控制）
- **Star style**：`Top of bar`（柱顶彩色星号，颜色对应检验组）或 `Bracket`（连接线 + 星号，按跨度升序排列分层）

### Prism Export — Grouped chart：Welch's t-test 星号标注
- 同 Column，比较单位为每个 row cluster 内各列组之间
- **Comparison**：`vs Control col`（每列 vs 指定 control 列）或 `All col pairs`（所有列组两两比较）
- **Control column**：动态下拉，填充列组名
- Correction family = 整张图所有 row × pair 检验（如 5 行 × 3 对 = 15 次共同校正）
- Bracket 模式每个 cluster 内独立分层，层间距 14px

### Prism Export — XY chart
- 新增 **MSE from:** 选项：`All groups (Prism)`（汇总所有组 MSE，与 Prism/emmeans 行为一致，df 更大、检验力更高）或 `Per-pair only`（仅对应两组重新计算 MSE，不假设方差齐性）
- `0 in log as` 控件移至 `pxy-anova-ctrl-row` 首位，改为**仅在 Log₁₀ 勾选时显示**（原先跟随 4PL checkbox，逻辑不正确）
- XY / Column / Grouped 三个 tab 的 Filter col 行中，`≠ value` 与 `Separator` 之间增加 `|` 竖线分隔符
- `pxy-anova-ctrl-row` 元素间距从 6px 增大至 14px

### Statistics Table — p 值多重校正
- **Assay Window** 新增 `Correction (p):` 下拉（None / Holm / BH）：对每张 Plate 内所有非 control 组的 Welch t-test p 值进行校正，`p_value` 列和 `p_star` 列同步更新
- **Disease Window** 新增相同的 `Correction (p):` 下拉

---

## 2026-05-15

### Statistics Table
- 三个 Tab（Assay / Disease / Dose Group）的结果表格顶部各加一条分隔线，与控件区域视觉分隔
- Outlier removal 下拉框宽度从 `min-width:180px` 改为 `min-width:120px`，与其他下拉框宽度一致
- "Run group analysis" 按钮上间距增大（`margin-top:18px`）、下间距缩小（`margin-bottom:6px`）
- "Run group analysis" 按钮高度通过 `.stat-tab-pane .btn:not(.btn-sm){padding:10px 20px;}` 与下拉框对齐

### 按钮位移修复
- **Export CSV**（Merged Data Table）、**📋 Copy to Prism**、**Download .csv** 三个按钮点击后不再移位：在每个按钮旁预置固定 `<span>` 消息元素（`id=btn*Msg`），`_btnWarn` 优先写入固定元素，不再动态插入节点


### 代码清理（Dead Code 移除）
- 删除 `buildWellSelUI()`：已被 `populateMergeKeySelects()` 取代，从未被调用
- 删除 `wellRadioChange()` / `manualWellChange()`：legacy aliases，从未被调用
- 删除 `RESULT_KEY_RE` 常量：定义后从未被引用
- 删除 `selectPlate()`：self-labeled legacy shim，已被 `_selectPlateFor()` 取代
- 删除 `renderStatTable()` wrapper：直接调用 `renderStatTables()`
- 删除 `state.resultData` 字段：写入 3 处但从未被读取，实际 merge 使用 `state.resultFiles`
- 删除 `resultFileList` DOM 查询：对应元素不存在于 HTML，静默无效
- 删除 `doMerge` 中的 `wellWarnings[]`：数组被写入但从未被显示或使用
- 修复 full-merge 路径中遗漏的 `renderBarChart()` 调用

### Bug 修复
- **异步解析竞态条件**：`handleResultFiles` 改为预分配 `new Array(files.length)` 后按索引赋值，确保多文件解析完成顺序不影响 `state.resultFiles` 数组与用户选择顺序的对应关系
- **新 metadata 文件旧列残留**：`handleDesign` 开头加 `state.designColRows = null`，上传新文件时立即清除旧列选择

### Plate 匹配逻辑重构
- 移除失效的 `plateMatchMode` 机制（`plateMismatchCard` 元素不存在，导致以前选了 Plate 列也从未真正按 Plate 匹配）
- 改为自动判断：每个 Result 文件独立计算 `fUsePlate = hasDesignPlate && !!fPlateCol`
- Metadata 选了 Plate 列 → Result 对应文件也选了 Plate 列：**Plate + Well 匹配**
- 任意一侧无 Plate 列：**Well-only 匹配**
- Metadata 有 Plate、Result 某文件无 Plate：实时在 Metadata Merge Key 卡片底部显示 inline 警告；merge 后在 `mergeWarnings` 区域列出具体文件名

### Merge Key UI
- Metadata Plate 列清空时，自动清除所有 Result 文件的 `plateCol` 并隐藏 Result 的 Plate 列选择框
- Metadata 未选 Plate 列时，Result 的 Plate 列选择框隐藏

### Prism Export
- Configure Columns 中 "Control group:" 和 "0 in log as" 行与上方 checkbox 行间距调整为 `10px`

---

## 2026-05-14

### UI / Layout
- Upload Files 页面重构为 2×3 网格布局：上传卡片 → Merge Key 卡片 → 预览卡片，左右两列各自对齐高度
- Merge Key 从 Step 2 Configure Columns 移至 Step 1 Upload Files，分为 Metadata / Result 两张独立卡片，上传文件后才显示
- Metadata Preview 与 Result Preview 表格起始位置对齐（统一 header 高度）
- 删除 Metadata File 和 Result File(s) 卡片中原有的 Well/Plate 列选择框（改由 Merge Key 卡片统一管理）

### Merge Key
- Well 列质检结果（✔ 240/240, 100%）改为内联显示在选择框右侧，不再单独成行

### Export
- 导出文件名改为日期格式（YYYYMMDD）：
  - `YYYYMMDD_Assay_Win.csv` / `YYYYMMDD_Disease_Win.csv`
  - `YYYYMMDD_EC50.csv` / `YYYYMMDD_ANOVA.csv` / `YYYYMMDD_EC50_ANOVA.csv`
- Heatmap 新增 **Export Figure** 按钮，将当前热图绘制到 Canvas 后导出为 `YYYYMMDD_HeatMap.png`
- Bar Chart 新增 **Export Figure** 按钮，导出当前图表为 `YYYYMMDD_BarChart.png`

### Configure Columns
- Small / Large / Export Stats CSV 按钮 padding 统一调整为 8px
- Metadata Columns 点击 **Select All** 后，Output name 字段自动填入原列名（而非留空）
- Columns to Merge 列头标签与下方选择框/输入框左对齐

### Load Template
- 简化模板加载逻辑：仅加载示例数据，不再预设列别名、Omit 列等配置，行为与手动上传文件一致
