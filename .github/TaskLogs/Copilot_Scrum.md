# !!!SCRUM!!!

# DESIGN REQUEST

Now you need to implement thee as a test file. #file:资产负债表--江苏润城城市投资控股集团有限公司.xlsx  You need to first design a suitable tree data structure to store the parsed data. Note that the tree structure in the original table requires you to parse the indentation amount to judge. This data structure also needs to be able to support custom constraints, such as observing a subtree separately, possibly parent and child nodes ("应收票据及应收账款" in the test file are the parent nodes of "应收票据" and "应收账款", and there is also a constraint between them (the parent node is the sum of the two).) The behavior of the constraint conditions should be able to choose warnings or custom behaviors (for example, if the parent node is not empty and the child nodes are all empty, then the value of the parent node needs to be assigned to the first child node in order). Please complete excel parsing, tree structure organization, and decoupled customizable constraint framework first.

# UPDATES

## UPDATE

1. TASK No.5 应作为TASK 4的自定义动作的一部分(CUSTOM)，作为一个预先实现的自定义动作。
2.  #file:account_taxonomy_level1_level2.yaml 是一个标准化的会计准则表，你需要在解析完成excel的TASK之后新增一个标准化的步骤，把原来节点的标签内容，查找词表替换成标准化的会计准则表，如果没有匹配到标准会计准则表，则需要报error并提示用户把这个词表新增到标准会计准则表中标准科目下的synonyms字段中，重新解析，这样解析后的所有节点label值都是这个yaml文件中的元素，完成标准化之后才能进行约束。

# TASKS

- [ ] TASK No.1: 设计并实现 Excel 行解析模型与缩进层级提取
- [ ] TASK No.2: 在解析后新增“标准会计科目标签标准化”步骤（基于 `REPO_ROOT/assets/account_taxonomy_level1_level2.yaml`）
- [ ] TASK No.3: 设计并实现可查询的财务树结构与子树访问接口
- [ ] TASK No.4: 设计并实现解耦约束框架与预置 CUSTOM 动作
- [ ] TASK No.5: 实现内置 SUM 约束并接入 TASK No.4 的预置 CUSTOM 动作
- [ ] TASK No.6: 组装执行编排（解析→标准化→建树→约束执行）并输出诊断事件
- [ ] TASK No.7: 为解析、标准化、树结构与约束引擎补充 pytest 测试

## TASK No.1: 设计并实现 Excel 行解析模型与缩进层级提取

本任务聚焦于把原始 Excel 行数据转换为结构化“可建树输入”，包括科目名称、原始值、多期值字段（若存在）、原始行号、缩进信息与推导层级。由于原始财务表使用缩进表达父子关系，必须先建立稳定、可测试的缩进解析规则，避免后续建树和约束判断出现歧义。

### what to be done

- 读取目标工作簿并定位有效数据区域（标题、空行、合计说明行可过滤）。
- 定义行级数据模型（例如 dataclass），字段至少覆盖 name_raw、values、row_index、indent、level。
- 实现缩进到层级的规则化转换，支持可配置缩进单位（如 2 空格、4 空格）。
- 明确异常输入处理：缩进跳级、名称为空但数值存在、全空行等。
- 产出可供标准化与建树阶段消费的标准行序列。

### rationale

- 树结构正确性的前提是层级提取稳定；先把“行解析”与后续流程解耦，有助于定位问题与独立测试。
- 知识库 KB_Design_TreeStructure.md 明确了缩进是层级语义来源，应将缩进规则固化为独立组件。
- 为后续标准化与约束框架提供统一输入形态，避免直接依赖 Excel 细节。

## TASK No.2: 在解析后新增“标准会计科目标签标准化”步骤（基于 account_taxonomy_level1_level2.yaml）

本任务将解析出的节点标签统一映射到标准会计科目词表，保证后续建树和约束仅处理标准标签。该步骤是约束执行前置条件。

### what to be done

- 读取 assets/account_taxonomy_level1_level2.yaml 并解析标准科目与 synonyms。
- 构建同义词到标准标签的查找索引。
- 对解析后的每个节点标签执行映射替换，使节点 label 落在 YAML 标准元素集合内。
- 对未命中标签抛出 error，并明确提示用户将该词补充到标准科目的 synonyms 字段后重新解析。
- 任一未命中时，阻断后续建树与约束执行。

### rationale

- 这是最新 UPDATE 的硬性要求，且直接决定约束是否可稳定执行。
- 标准化先行可避免名称变体造成的约束匹配漂移。
- 错误信息直接指向词表维护动作，形成可执行闭环。

## TASK No.3: 设计并实现可查询的财务树结构与子树访问接口

本任务将标准化后的行序列转换为树，并提供节点查询、父子遍历、子树切片等能力，以支持“只观察某个子树约束”的需求。数据结构需对多种约束场景友好，且可追溯原始 Excel 行。

### what to be done

- 定义树节点模型（节点 ID、标准标签、父节点、子节点、层级、数值容器、元数据）。
- 基于行序列按层级构建树，保证顺序稳定且可检测不合法父子关系。
- 提供核心访问 API：按名称/ID 查节点、获取祖先链、获取子树节点集、按层遍历。
- 明确同名节点处理策略（必要时使用路径或行号消歧）。
- 确保树节点与原始行可双向追踪（便于报错定位与测试断言）。

### rationale

- 你的需求明确提出“可单独观察子树”和“父子关系约束”，这要求树结构必须支持作用域化查询。
- 把数据结构设计为通用节点模型，能让约束引擎与 Excel 解耦，满足后续扩展自定义约束。
- 完成树接口后，约束层可只依赖树 API，降低耦合。

## TASK No.4: 设计并实现解耦约束框架与预置 CUSTOM 动作

本任务建立约束系统可扩展骨架，使约束定义、执行结果与行为策略分离，并将“父有值且子全空时按顺位分配到首子节点”作为预置 CUSTOM 动作实现到框架中（不再独立拆任务）。

### what to be done

- 定义约束接口（输入作用域节点集与上下文，输出检查结果）。
- 定义行为策略接口（WARNING、CUSTOM 等）并与约束触发条件解耦。
- 定义执行结果模型：是否命中、诊断信息、事件列表、是否修改数据。
- 定义约束注册与执行机制（可按全树/子树选择执行）。
- 实现预置 CUSTOM 动作：父节点非空且子节点全集为空时，将父值按顺位写入首子节点，并记录变更事件。

### rationale

- 知识库 KB_Design_ConstraintSystem.md 强调约束类型与修复策略分离，本任务用于落地该设计原则。
- 按 UPDATE 要求将原 TASK No.5 并入 TASK No.4，有助于把行为能力下沉到框架层。
- 预置 CUSTOM 动作形成默认可用能力，同时保留后续扩展空间。

## TASK No.5: 实现内置 SUM 约束并接入 TASK No.4 的预置 CUSTOM 动作

本任务实现核心 SUM 关系：父节点值应等于直接子节点值之和，并在命中策略时调用 TASK No.4 中已实现的预置 CUSTOM 动作。

### what to be done

- 实现 SUM 约束：读取父节点与直接子节点值，计算并比较（支持容差）。
- 提供策略分支：
  - WARNING：只记录告警，不改值。
  - CUSTOM：触发框架中的预置动作处理器。
- 支持约束作用域绑定到指定父节点（如“应收票据及应收账款”子树）。
- 统一输出诊断信息：节点路径、原值、计算值、偏差、策略执行结果。

### rationale

- SUM 是最小可验证业务闭环，且对应你提供的父子示例。
- 将动作执行委托给 TASK No.4 的框架能力，可保持规则层简洁。
- 作用域绑定能力可直接支持“只观察某个子树”的业务场景。

## TASK No.6: 组装执行编排（解析→标准化→建树→约束执行）并输出诊断事件

本任务把前述组件串联为可运行流程，形成端到端最小可用链路，支持对目标测试 Excel 一次执行得到结构与约束结果。

### what to be done

- 增加统一入口：执行 Excel 解析、标准化、树构建、约束注册、约束执行。
- 强制顺序约束：标准化成功后才允许进入约束执行阶段。
- 约束执行支持指定作用域（全树或指定节点子树）。
- 汇总输出：错误、告警、动作执行记录、节点值变更事件。

### rationale

- 用户目标是“先完成解析+树+可定制约束框架”，需要可运行链路证明组件可协作。
- 将标准化明确前置可避免词表未对齐导致的约束误判。
- 完整诊断输出可降低调试成本，并为测试任务提供可断言目标。

## TASK No.7: 为解析、标准化、树结构与约束引擎补充 pytest 测试

本任务专注测试，不混入功能实现。测试范围覆盖解析正确性、标准化命中与未命中、树关系正确性、SUM 检查逻辑、策略分支行为与预置 CUSTOM 动作。

### what to be done

- 新增解析层测试：缩进解析、异常行过滤、层级推导。
- 新增标准化测试：同义词命中替换、未命中报错与提示信息。
- 新增树结构测试：父子关系、子树提取、同名消歧（若实现）。
- 新增约束测试：SUM 命中与不命中、WARNING 与 CUSTOM 行为分支。
- 新增动作测试：父非空且子全空时值写入首子节点，事件日志完整。

### rationale

- 按要求将“测试”单独作为任务，避免和功能开发混写。
- 该项目核心价值在规则正确性与可审计性，测试应优先覆盖约束行为边界。
- 标准化新增为关键前置步骤，必须单独覆盖其失败阻断语义。

# Impact to the Knowledge Base

## Finance Normalizer

- 需要新增或更新 Project/Choosing APIs：
  - Excel 行解析模型、标准化映射 API、树查询 API、约束执行器 API 的组合方式。
- 需要新增或更新 Project/Design Explanation：
  - 执行顺序“解析→标准化→建树→约束”的门禁语义。
  - 约束框架中 Constraint 与 Action 的解耦设计。
  - 预置 CUSTOM 动作（父有值且子全空时顺位赋值）的触发条件与行为约定。
  - 作用域化约束执行（全树/子树）与诊断事件模型。
- 若后续实现约束配置文件（YAML），应补充 WARNING/CUSTOM 声明方式与执行顺序说明。

# !!!FINISHED!!!
