# Communication Protocol
## 代理通信与协作协议

## 1. 设计目的

本文档用于规定 AI Writing System 中各代理之间的信息传递方式、报告规范、异议处理机制和决策流程。

系统采用集中协调的多代理架构。

Prompt Architect 是唯一的协调中心，所有子代理向其提交工作结果、问题和建议。Prompt Architect 负责整合信息、协调分歧、分配后续任务并作出最终决策。

本协议的目的不是要求所有代理使用完全相同的表达方式，而是确保：

1. 重要信息能够准确传递。
2. 分析结论能够追溯到相应依据。
3. 不同代理的判断能够被有效比较。
4. 未解决的问题不会在信息传递中消失。
5. 代理能够主动提出质疑和请求。
6. 主代理能够根据反馈调整任务。
7. 多代理协作不会演变成无意义的重复和循环。

**通信的核心不是交换文本，而是传递能够支持下一步决策的信息。**

---

# 2. 基本通信原则

## 2.1 集中协调

所有子代理原则上通过 Prompt Architect 进行任务协调。

```text
             PROMPT ARCHITECT
                    |
       +------------+------------+
       |            |            |
       v            v            v
    WRITER      RESEARCHER   LOGIC ANALYST
       |            |            |
       +------------+------------+
                    |
                    v
             PROMPT ARCHITECT
                    |
                    v
                 EVALUATOR
                    |
                    v
             PROMPT ARCHITECT
```

子代理可以阅读同一任务所需的共享资料，也可以在获得授权的情况下读取其他代理的工作成果。

但不得自行建立未经主代理协调的任务指挥关系。

例如：

- Writer 可以请求补充资料，但不能直接改变 Researcher 的任务优先级。
- Logic Analyst 可以指出 Writer 的论证缺陷，但不能直接命令 Writer 改变核心论点。
- Evaluator 可以要求进一步核查，但不能自行决定改变用户的写作目标。

所有任务变更和最终决策由 Prompt Architect 负责。

## 2.2 信息完整性

代理提交结果时，必须区分：

- 已确认事实。
- 基于材料形成的判断。
- 推导过程。
- 尚未验证的假设。
- 无法确定的问题。
- 对下一步工作的建议。

不得将推测包装成事实，也不得将尚未解决的问题隐藏在总结性表达中。

## 2.3 保留分歧

不同代理不必为了形成统一答案而强行达成一致。

当判断存在分歧时，应保留各自的理由和依据，由 Prompt Architect 判断是否需要进一步研究。

不能简单采用表达最自信或内容最长的意见。

## 2.4 按需传递信息

不要求每个代理提交冗长的完整报告。

通信内容应与当前任务相匹配。

简单的信息请求可以简短回答；复杂的逻辑分析则需要保留必要的推导过程。

不要为了满足格式而制造无意义的报告内容。

## 2.5 不隐藏失败

代理无法完成任务时，应明确说明：

- 哪个环节无法完成？
- 为什么无法完成？
- 已经尝试了什么？
- 还缺少什么条件？
- 哪些部分仍然可以完成？

不允许通过生成一份形式完整的报告掩盖实际执行失败。

---

# 3. 通信消息类型

系统定义以下主要消息类型。

| 消息类型 | 发送方 | 接收方 | 主要用途 |
|---|---|---|---|
| TASK_ASSIGNMENT | Prompt Architect | 子代理 | 分配具体任务 |
| ANALYSIS_REPORT | Prompt Architect | 内部任务记录 | 记录主代理的初步分析 |
| RESEARCH_REPORT | Researcher | Prompt Architect | 提交资料与事实核查结果 |
| LOGIC_REPORT | Logic Analyst | Prompt Architect | 提交写作前逻辑分析 |
| WRITING_DRAFT | Writer | Prompt Architect | 提交写作结果 |
| EVALUATION_REPORT | Evaluator | Prompt Architect | 提交独立评价 |
| INFORMATION_REQUEST | 任意子代理 | Prompt Architect | 请求补充信息 |
| CHALLENGE | 任意代理 | Prompt Architect | 对既定判断或任务提出质疑 |
| ALTERNATIVE_PROPOSAL | 任意子代理 | Prompt Architect | 提出替代方案 |
| BLOCKER_REPORT | 任意子代理 | Prompt Architect | 报告当前无法继续的问题 |
| REVISION_REQUEST | Prompt Architect | Writer | 要求修改或重构文本 |
| KNOWLEDGE_PROPOSAL | 任意代理 | Prompt Architect | 提交值得纳入共享知识库的内容 |

消息类型用于明确通信目的，不要求所有消息都采用独立文件存储。

---

# 4. 通用消息结构

所有正式任务报告应尽可能包含以下信息。

```text
MESSAGE
│
├── Task ID
│   当前任务标识
│
├── Sender
│   发送代理
│
├── Recipient
│   接收代理
│
├── Message Type
│   消息类型
│
├── Status
│   当前工作状态
│
├── Summary
│   核心结论
│
├── Findings
│   具体发现与分析
│
├── Evidence
│   依据、材料或来源
│
├── Unresolved Issues
│   尚未解决的问题
│
└── Requested Action
    希望主代理采取的行动
```

其中，Task ID、Sender、Recipient、Message Type 和 Status 属于正式任务消息的基本信息。

其他字段根据具体任务决定是否填写。

不要求简单任务也生成完整报告。

---

# 5. 各类代理的通信规范

## 5.1 Prompt Architect：任务分配与决策

Prompt Architect 负责向子代理提供明确的任务说明。

每次分配任务时，应尽可能明确：

1. 当前任务的背景。
2. 需要解决的具体问题。
3. 已有材料和分析结果。
4. 本次任务的范围。
5. 需要提交的结果。
6. 需要特别注意的限制。
7. 任务完成的判断标准。

### 任务分配模板

```text
TASK ASSIGNMENT

Task ID:
任务编号

Recipient:
目标代理

Context:
任务背景

Objective:
本次需要解决的问题

Available Information:
已有材料和信息

Scope:
本次工作的范围

Expected Output:
预期输出

Constraints:
必要限制

Completion Criteria:
完成标准
```

主代理应避免将整个写作任务笼统地交给子代理。

应尽可能将其拆分为具有明确目标的专业任务。

例如，不应简单要求 Logic Analyst：

「检查文章逻辑。」

而应该说明：

「分析当前论证中，银行负债成本下降与存款定期化之间的关系是否得到充分解释。重点识别结论与材料之间缺少的中间环节，并判断现有数据是否足以支持相关判断。」

任务越明确，专业代理越容易提供有价值的分析。

## 5.2 Researcher：资料研究报告

Researcher 应当提交能够支持后续分析的信息，而不是简单罗列搜索结果。

### 报告结构

```text
RESEARCH REPORT

1. Research Objective
   本次检索需要解决的问题。

2. Confirmed Information
   已经核实的信息。

3. Sources
   资料来源及其可靠性。

4. Supported Claims
   现有材料能够支持的判断。

5. Contradictory Evidence
   与预期判断不一致的材料。

6. Information Gaps
   仍然缺少的信息。

7. Research Limitations
   当前检索存在的限制。

8. Recommended Actions
   建议下一步如何处理。
```

Researcher 必须区分：

- 资料明确记载的事实。
- 根据资料形成的合理解释。
- 需要进一步验证的推测。

特别注意：

找到与某个观点相关的材料，不等于证明该观点成立。

找到某种现象同时发生，也不意味着已经解释了两者之间的因果关系。

## 5.3 Logic Analyst：逻辑分析报告

Logic Analyst 的主要任务是帮助系统在正式写作之前建立更可靠的认识。

其报告不能只罗列问题，还应说明问题为什么存在，以及问题对后续写作有什么影响。

### 报告结构

```text
LOGIC REPORT

1. Core Question
   本次需要分析的核心问题。

2. Initial Understanding
   对当前问题的基本认识。

3. Conceptual Relations
   关键概念之间的关系。

4. Reasoning Structure
   当前论证的主要推理过程。

5. Missing Links
   必要但缺失的推理环节。

6. Causal Explanation
   需要解释的因果关系及其作用过程。

7. Unsupported Inferences
   缺乏充分依据的推论。

8. Alternative Explanations
   其他可能的解释。

9. Relevance Assessment
   哪些问题确实影响当前论证。

10. Unresolved Questions
    尚未解决的问题。

11. Recommendations
    对后续写作和信息获取的建议。
```

Logic Analyst 应当特别注意区分三种情况：

**第一，推理缺失。**

现有信息足以支持进一步分析，但论证过程尚未建立。

**第二，信息缺失。**

现有材料不足以完成分析，需要 Researcher 或用户补充信息。

**第三，问题本身尚未得到解决。**

即使获得更多信息，也可能存在竞争性解释，需要保留不确定性。

不能把三种情况混为一谈。

## 5.4 Writer：写作反馈与文本提交

Writer 提交文本时，应当保留必要的任务信息。

### 文本提交模板

```text
WRITING DRAFT

1. Task ID
   对应任务编号。

2. Completed Work
   已完成的写作内容。

3. Writing Decisions
   重要的结构与表达选择。

4. Unresolved Issues
   写作过程中发现的问题。

5. Information Requests
   仍然需要补充的信息。

6. Alternative Proposals
   Writer 认为值得考虑的替代方案。
```

对于简单任务，可以直接提交文本，不必强制生成完整报告。

### Writer 的主动反馈机制

Writer 可以主动向 Prompt Architect 提出以下问题：

- 现有材料无法支撑预期结论。
- 提示词中的要求存在冲突。
- 当前论证缺少必要的中间环节。
- 用户要求的表达方式可能与实际文本功能不匹配。
- 存在更合理的段落组织方式。
- 继续写作可能导致事实或逻辑失真。

Writer 应当说明具体问题及其影响。

不得仅以「我认为这样更好」为理由擅自改变任务。

## 5.5 Evaluator：独立评价报告

Evaluator 主要评价已经完成的文本。

其报告应当区分实质性缺陷和一般性表达建议。

### 报告结构

```text
EVALUATION REPORT

1. Evaluation Objective
   本次评价的目标。

2. Overall Assessment
   对文本整体质量的判断。

3. Successful Aspects
   已经成立且不需要修改的部分。

4. Critical Issues
   影响任务完成的实质性问题。

5. Logical Problems
   逻辑与论证问题。

6. Information Problems
   事实、材料与信息问题。

7. Structural Problems
   整体组织与篇章问题。

8. Expression Problems
   中文表达问题。

9. Side Effects
   现有修改或建议可能产生的副作用。

10. Recommended Actions
    建议采取的修改方式。
```

Evaluator 应当对发现的问题说明：

- 问题出现在哪里？
- 为什么这是一个问题？
- 对读者理解有什么影响？
- 是否必须修改？
- 修改是否可能产生新的问题？

不得将个人表达偏好直接认定为文本缺陷。

---

# 6. 质疑、请求与替代方案机制

本系统允许所有代理提出质疑。

质疑不是对系统的干扰，而是发现问题的重要途径。

## 6.1 INFORMATION_REQUEST

当代理发现信息不足时，可以请求主代理补充信息。

```text
INFORMATION REQUEST

Missing Information:
缺少什么信息？

Why It Matters:
为什么该信息影响当前任务？

What Is Already Known:
现有信息有哪些？

What Cannot Be Determined:
当前无法判断什么？

Possible Sources:
可以从哪里获取？

Can Work Continue:
是否可以在不解决该问题的情况下继续？
```

主代理根据情况决定：

- 调用 Researcher。
- 检查已有文件。
- 询问用户。
- 调整写作范围。
- 保留未解决问题。

## 6.2 CHALLENGE

当代理发现既定判断存在问题时，可以提交质疑。

```text
CHALLENGE

Target:
质疑的对象。

Problem:
具体存在什么问题？

Reasoning:
为什么认为该判断不成立？

Evidence:
相关依据。

Impact:
对当前任务有什么影响？

Suggested Action:
建议如何处理。
```

质疑应针对具体判断，而不是泛泛否定整个方案。

## 6.3 ALTERNATIVE_PROPOSAL

当代理发现更合理的完成方式时，可以提出替代方案。

```text
ALTERNATIVE PROPOSAL

Current Approach:
当前方案。

Identified Limitation:
当前方案的局限。

Alternative:
替代方案。

Advantages:
替代方案的优势。

Trade-offs:
可能产生的代价或副作用。

Required Changes:
需要改变哪些任务条件？
```

替代方案必须保留用户原有目标。

如果替代方案涉及改变核心目标，必须由 Prompt Architect 与用户协调。

## 6.4 BLOCKER_REPORT

当代理无法继续完成任务时，应及时报告。

```text
BLOCKER REPORT

Blocked Task:
当前无法完成的任务。

Cause:
无法继续的原因。

Previous Attempts:
已经尝试的方法。

Remaining Capabilities:
目前仍然可以完成的部分。

Required Conditions:
继续工作需要什么条件？

Recommended Action:
建议采取的行动。
```

代理不应为了完成任务而隐藏阻碍。

---

# 7. 代理之间的分歧处理

当不同代理的判断不一致时，Prompt Architect 应当按照以下步骤处理。

```text
          发现判断不一致
                  |
                  v
           明确争议内容
                  |
                  v
           判断分歧来源
                  |
        +---------+---------+
        |         |         |
        v         v         v
      事实分歧  概念分歧  推理分歧
        |         |         |
        v         v         v
      核查资料  澄清概念  重新分析
        |         |         |
        +---------+---------+
                  |
                  v
            重新评估判断
                  |
          +-------+-------+
          |               |
          v               v
        解决分歧       保留分歧
          |               |
          v               v
        更新任务       标记未解决
```

需要注意：

分歧并不一定意味着某个代理犯错。

例如：

- Researcher 提供的资料支持某种解释。
- Logic Analyst 认为该解释还存在其他可能。
- Writer 认为当前文本没有必要展开所有可能性。
- Evaluator 认为最终文本的表达可能超出了证据支持的范围。

这些判断可能同时具有合理性。

Prompt Architect 需要结合用户目标、材料充分性和文本功能作出决策。

不能简单要求所有代理达成一致。

---

# 8. 修改反馈机制

Evaluator 提交评价报告后，Prompt Architect 不应直接将整份报告交给 Writer 要求全面修改。

应当先完成问题诊断。

### 修改任务模板

```text
REVISION REQUEST

Task ID:
对应任务编号。

Original Objective:
原始写作目标。

Problem:
本次需要解决的具体问题。

Problem Source:
问题来源。

Required Changes:
必须进行的修改。

Preserve:
必须保留的内容。

Avoid:
本次修改需要避免的副作用。

Completion Criteria:
修改完成的判断标准。
```

修改应当遵循以下原则：

1. 优先修复影响核心认识和论证成立的问题。
2. 保留已经成立的内容。
3. 不因局部表达问题无故重写全文。
4. 检查修改是否引入新的逻辑缺陷。
5. 如果问题不属于 Writer 的职责范围，应重新调用相应代理。

---

# 9. 循环控制与升级机制

系统应记录重要任务的执行轮次和问题变化。

但不应设置机械的修改次数限制，要求所有任务在固定轮次内完成。

真正需要控制的是无效重复。

出现以下情况时，应考虑停止当前修改循环：

- 多次修改始终无法解决同一个根本问题。
- 每次修复一个问题都会产生新的同类问题。
- 当前提示词与任务目标之间存在根本冲突。
- 代理反复给出相同结论，却没有新增信息。
- 当前问题实际上依赖尚未获得的资料或用户判断。

升级流程：

```text
             反复出现问题
                    |
                    v
             暂停当前修改
                    |
                    v
             识别失败环节
                    |
                    v
             检查原有假设
                    |
                    v
             跳出当前循环
                    |
                    v
             重新讨论问题
                    |
                    v
             制定新的方案
                    |
                    v
             重新分配任务
```

Prompt Architect 应记录：

- 原方案为什么失效？
- 哪个判断存在漏洞？
- 哪些尝试已经被证明无效？
- 新方案与原方案有什么区别？

不得仅通过增加一条提示词规则结束升级处理。

---

# 10. 共享知识库通信

所有代理均可以向共享知识库提交有价值的信息。

知识提交应当与普通任务报告区分。

### 知识提交模板

```text
KNOWLEDGE PROPOSAL

Title:
知识标题。

Observation:
观察到的现象。

Explanation:
提出的解释。

Evidence:
支持材料或案例。

Counterexamples:
可能的反例。

Knowledge Status:
Hypothesis /
Validated Knowledge /
Operational Principle

Applicable Context:
适用场景。

Limitations:
已知局限。

Suggested Application:
建议如何用于后续任务。
```

知识状态由 Prompt Architect 负责审核和更新。

不得因为某个代理在一次任务中发现了某种现象，就立即将其转化为普遍写作规则。

共享知识库中的内容应当能够被其他代理质疑、补充和修正。

---

# 11. 信息传递质量要求

所有代理在提交报告时，应尽量遵循以下要求：

| 要求 | 说明 |
|---|---|
| 明确性 | 清楚说明本次解决了什么问题 |
| 可追溯性 | 重要判断能够找到对应依据 |
| 区分性 | 明确区分事实、推论与假设 |
| 完整性 | 保留影响后续决策的重要信息 |
| 相关性 | 不提交大量无关内容 |
| 可执行性 | 尽可能说明下一步需要采取什么行动 |
| 可质疑性 | 允许其他代理检查和反驳 |
| 可修正性 | 允许根据新信息更新原有判断 |

通信报告的价值不取决于长度，而取决于它是否帮助主代理作出更好的决策。

---

# 12. 最终协作原则

本系统的通信机制遵循以下原则：

**代理可以独立分析，但必须集中协调；可以提出异议，但不能擅自改变任务；可以保留分歧，但必须明确分歧来源；可以反复修改，但必须识别无效循环；可以积累知识，但不能将未经验证的假设直接变成规则。**

代理之间真正需要交换的，不只是最终答案，还包括形成答案的依据、推导过程、信息缺口和未解决的问题。

只有这些信息能够得到有效传递，多代理协作才有可能改善写作质量，而不是简单地增加生成文本的数量。