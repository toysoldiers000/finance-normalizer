
# Finance Statement Normalizer（财务报表标准化与约束修复）

> 目标：把来源不一致、科目名称存在变体、层级与加和关系复杂的财务 Excel 表格，**清洗、标准化并迁移**到一个**无歧义的标准模板**。  
> 核心方法：**树结构（缩进层级） + 数据关系约束（SUM/EQUAL 等） + 可配置修复策略（Top-down 回填） + 术语对齐（词表对齐/实体规范化）**。

---

## 这个仓库解决什么问题？

现实中的财务报表 Excel 常见特点：

1. **层级结构由缩进表达**  
   - 无缩进：父节点  
   - 2 个空格：子节点  
   - 4 个空格：孙子节点  
   - ……

2. **默认存在加和关系（约束）**  
   - 父节点 = 若干子节点之和  
   - 子节点 = 若干孙子节点之和  
   - 不同父节点下，子节点层级不一定齐整（可能混合等级）

3. **存在“优先级回填”修复需求**  
   - 当某些子节点缺失值时，需要把父节点的值按策略向下分配  
   - 例如：按照子节点优先级，把父节点剩余值迁移到最高优先级的缺失子节点  
   - 或按平均/权重分配（可配置）

4. **可能存在“恒等关系”**  
   - 某些父节点只有一个子节点，二者本质上可能恒等（EQUAL），无差异  
   - 缺失时应直接回填；冲突时需报告或按策略裁决

5. **科目名称存在大量中文变体，且事先无法穷举**  
   - 例如：`固定资产` / `固定的资产` / `固定资产（净额）` / `固定资产—原值`  
   - 仅增加“的、与、和、或”等助词/介词就会导致映射失败  
   - 因而需要：**实体对齐/术语规范化（词表对齐）** + **可演进别名库** + **人工审核闭环**

本仓库提供一个工程化框架，把上述问题拆成两个独立引擎并通过编排层串起来。两个独立引擎可以独立工作也可以联合工作。除了这两个引擎，还留有扩展其他引擎的功能，比如生成引擎等等：

- **约束修复引擎（Constraint & Repair Engine）**：只关注结构与数值关系，不依赖语言语义
- **术语对齐引擎（Terminology Alignment Engine）**：只负责科目名称 → 标准科目 ID，对齐结果可审计、可迭代
- **编排层（Orchestrator）**：导入 Excel → 对齐 → 修复 → 生成引擎 → (其他可扩展引擎) + 报告

---

## 你会在这里得到什么输出？

一次完整跑批通常产出：

1. 标准模板数据（已映射为 `std_id`，并修复过缺失/不一致）
2. 数据质量报告（约束校验是否通过、容差范围）
3. 变更日志（每一步修复了什么、为什么修复、由哪条规则触发）
4. 映射待审核队列（未命中的科目名 + top-k 候选 + 置信度/证据）
5. 冲突列表（EQUAL/SUM 约束冲突、需要人工裁决）

---

## 关键概念与术语

### 1) 节点（Node）
Excel 每一行（科目）是一个节点，包含：
- `name_raw`：原始科目名称
- `name_norm`：规范化后的名称（用于查表/检索）
- `level`：缩进层级
- `parent_id`：父节点引用
- `priority`：同级子节点优先级（用于 top-down 回填策略）
- `values`：一个或多个数值字段（可多期、多口径）#TODO: fix this values可能是字典类型，对应多期，多年份等等

### 2) 标准科目（Canonical Account）
用 **稳定的标准 ID**（`std_id`）表示一个标准科目，名称只是展示：
- `std_id`（PK）
- `std_name`
- `parent_std_id`（标准模板树）
- `meta`（JSON 扩展元信息）

> 重要：标准化依赖 `std_id`，而不是中文名称的唯一性。

### 3) 约束（Constraint）
约束定义数值关系，例如：
- `SUM`：父 = Σ子
- `EQUAL`：父 ≡ 子
- `CUSTOM`：自定义约束（以插件方式扩展）

### 4) 修复策略（Repair Strategy）
当发现缺失/不一致时如何处理，例如：
- `TOP_DOWN_PRIORITY`：从父往下按优先级回填
- `AVERAGE`：平均分配
- `WEIGHTED`：按权重分配
- `COPY`：恒等回填
- `NONE`：只校验不修复（生成报告）
- `CUSTOM`：自定义策略（函数/插件）

---

## 软件架构概览

### 高层架构图（逻辑）

```
        +-------------------+
        |      Ingest       |
        | Excel -> NodeTree  |
        +---------+---------+
                  |
                  v
     +------------+-------------+
     | Terminology Alignment     |
     | name -> std_id           |
     |  - exact/alias lookup    |
     |  - fuzzy/vector candidates|
     |  - (optional) LLM assist |
     |  - human review queue    |
     +------------+-------------+
                  |
                  v
     +------------+-------------+
     | Constraint & Repair       |
     | tree + rules -> fixed data|
     |  - SUM/EQUAL constraints  |
     |  - bottom-up aggregation  |
     |  - top-down fill          |
     |  - iteration until stable |
     |  - change events log      |
     +------------+-------------+
                  |
                  v
        +---------+---------+
        |       Export      |
        | template + report |
        +-------------------+
```



### 分层原则（保持可扩展的关键）
- 术语对齐引擎与修复引擎**完全解耦**
- 修复引擎**不依赖 NLP**
- 所有自动更改**必须产生日志事件**
- 映射库与规则配置可版本化（推荐 Git + 数据库导出/导入）

---

## 推荐的目录结构（建议实现方式）

> 具体文件名可调整，但建议保持职责清晰。

```

repo/
README.md
finance_normalizer/
init.py
ingest/
  excel_reader.py          # 读取 Excel
  indent_parser.py         # 缩进 -> level
  tree_builder.py          # level -> parent_id -> tree

terminology/
  normalize.py             # 中文名称规范化（保守规则）
  lookup.py                # alias 精确命中
  candidates.py            # fuzzy/vector top-k 召回
  aligner.py               # 对齐编排：lookup -> candidates -> (llm) -> queue

constraints/
  engine.py                # 规则引擎：迭代执行直到收敛
  events.py                # Event 数据结构（变更日志）
  rules/
    sum_rule.py            # SUM 规则：聚合 + 校验
    equal_rule.py          # EQUAL 规则：回填 + 冲突检测
    top_down_fill.py       # top-down 回填策略
    custom_rule_template.py# 插件模板

storage/
  db.py                    # SQLite/Postgres 连接
  models.py                # std_account, account_alias 等表结构
  export_aliases.py        # 导出到 CSV/JSON 用于 Git 版本化

orchestration/
  pipeline.py              # 端到端 pipeline
  report.py                # 报告生成（未命中/冲突/事件）


configs/
constraints.yaml           # 约束定义（可按模板/客户分文件）
normalization.yaml         # 规范化规则（可选）
mapping_sources.yaml       # 数据来源配置（可选）

examples/
sample_input.xlsx
sample_std_accounts.csv
sample_aliases.csv

```

---

## 约束与修复引擎设计

### Rule 接口（核心扩展点）

所有规则实现同一个接口：

- 输入：`tree, data, context`
- 输出：`changed: bool, events: list[Event], diagnostics`

伪接口示意：

```text
Rule.apply(tree, data, context) -> ApplyResult
ApplyResult:
  - changed: bool
  - events: [Event]
  - violations: [Violation]
```

### Event（变更日志）

每次修复必须记录：

* `node_id / std_id`
* `field`（哪个数值列）
* `old_value -> new_value`
* `rule_id`
* `reason`
* `timestamp`

> 目的：可审计、可回放、可回滚。

### 迭代执行直到稳定（解决“顺序复杂”）

典型执行序列（每轮）：

1. bottom-up：能聚合的先聚合（父缺失但子齐全）
2. top-down：父已知，子缺失，按策略回填
3. equal：恒等回填与冲突检测
4. 约束校验：记录未满足项

重复直到：

* 本轮 `changed == false`（收敛）
* 或达到迭代上限（防死循环）

---

## 术语对齐（实体对齐 / 词表对齐）设计

### 为什么要“可演进映射库”？

因为变体不可穷举，正确姿势是把“新出现变体”变成资产：

* 初次出现：进入待审队列
* 审核通过：写回 alias 表
* 下次出现：确定性命中（稳定、可复现）

### 三段式决策链（建议默认）

1. **查表**（最高优先级、确定性）

* `alias_raw`/`alias_norm` 精确命中 → 返回 `std_id`

2. **候选召回**（不确定但可解释）

* RapidFuzz（字符相似）或向量检索（语义相似）返回 top-k

3. **(可选) LLM 辅助**

* 只用于：在 top-k 中排序、解释、或补充可能别名
* **必须强约束输出**：只能从候选集合中选择 `std_id` 或输出 `unknown`
* 不能让 LLM 直接“发明标准科目”

4. **人工确认**

* 将 `raw + top-k + 证据 + 置信度` 提交审核
* 审核通过写回 `account_alias`，并进入回归测试集

---

## 配置方式（保持简单与可扩展）

建议用 YAML/JSON 来定义“约束适用范围 + 修复策略”，例如：

* 适用范围：

  * 指定 `std_id`
  * 指定 level
  * 指定 tag/类别
  * 指定 pattern（谨慎使用）

* 约束类型：

  * SUM / EQUAL / CUSTOM

* 修复策略：

  * TOP_DOWN_PRIORITY / AVERAGE / WEIGHTED / COPY / NONE / CUSTOM

---

## 运行方式（示例）

> 以下为示意，实际命令以仓库实现为准。

```bash
# 1) 导入并跑全流程
python -m finance_normalizer.orchestration.pipeline \
  --input examples/sample_input.xlsx \
  --constraints configs/constraints.yaml \
  --std-accounts examples/sample_std_accounts.csv \
  --aliases examples/sample_aliases.csv \
  --output out/

# 2) 输出结果
# out/standardized.xlsx
# out/change_events.csv
# out/violations.json
# out/mapping_review_queue.csv
```

---

## 人工审核工作流（推荐）

1. 跑 pipeline 得到 `mapping_review_queue.csv`
2. 人审确认后更新 `account_alias`（数据库或 CSV）
3. 导出 alias 表到 Git（PR 审核）
4. 跑回归测试：历史样本映射结果不应漂移
5. 合并后发布

> 核心：把“不断出现的变体”变成可版本化资产，而不是散落在代码里的 if/else。

---

## 设计边界与原则

* **不试图从会计准则语义推导唯一名称**：我们只做“在数据宇宙中可维护的对齐”
* **标准化锚点是 std_id**，不是中文名称
* **修复引擎不做语义判断**：只执行可配置的数学/结构规则
* **所有自动修复都有证据链**（Event 日志）
* **LLM 不是裁决者**：只能辅助候选、解释与排序；最终映射由词表/别名库与人审决定

---

## 里程碑建议（最小可用到完整能力）

### M1：约束修复 MVP

* 缩进建树
* SUM/EQUAL 校验
* top-down 优先级回填
* 事件日志 + 报告

### M2：映射库闭环（无 LLM）

* std_account + account_alias（SQLite）
* normalize + 查表
* fuzzy top-k + 待审队列
* 人审回写 + 回归测试

### M3：向量/LLM 候选增强

* 向量检索提高召回
* LLM 约束输出，仅在不确定时介入
* 审核与版本化流程固化

---

## 贡献指南（对新同学最重要的扩展点）

### 1) 新增一种约束或修复策略

* 在 `finance_normalizer/constraints/rules/` 下新增一个 rule
* 实现 `Rule.apply(...)`
* 在 `constraints.yaml` 中声明适用范围与参数
* 为该规则添加单元测试（尤其是边界情况：缺失/冲突/容差）

### 2) 新增一种术语对齐策略

* 保持 `aligner.py` 的决策链顺序不变
* 新策略只做“候选召回/排序”，不要破坏确定性查表
* 所有自动建议必须可解释（返回 evidence/score）

---

## FAQ

### Q: 这是“分类任务”吗？

它可以被表述为“文本分类”，但更准确是：

* **实体对齐 / 术语规范化（canonicalization）**：把变体名称对齐到标准词表 `std_id`
  并且配合：
* **树 + 约束求解/迭代修复**：处理 SUM/EQUAL/回填等数值关系

### Q: 为什么不直接用大模型做映射？

因为需要：

* 可复现（同输入同输出）
* 可审计（为什么这么映射）
* 可回滚（映射库版本化）
  因此 LLM 只作为候选增强，而不是最终裁决。

---

## 联系与维护

* 约束规则问题：看 `configs/constraints.yaml` 与 `finance_normalizer/constraints/`
* 映射/别名维护：看 `storage/` 与导出的 `account_alias` 数据
* 端到端流程：看 `orchestration/pipeline.py`

欢迎新同学先从 `examples/` 的样例跑一遍，理解输出的报告与事件日志，再开始写规则或扩展对齐策略。

