# Agents

## 代理提示词层：接口约定与迁移清单

本目录存放各代理的**行为提示词**。它回答「这个代理是谁、什么时候动手、向谁交付、能决定什么」，
不存放写作知识，也不存放执行时序。

---

## 1. 三层职责边界

| 层 | 回答的问题 | 允许写什么 | 不允许写什么 |
|---|---|---|---|
| `agents/*.md` | 这个代理是谁、做什么、能决定什么 | 身份、任务、职责、触发条件、输入输出契约、权限、验收标准 | 写作方法正文、阶段性流程的完整叙述、知识条目 |
| `knowledge/**` | 写作与思考本身该怎么进行 | 概念、机制、判断标准、适用条件、反例 | 代理分工、调用时机、消息格式 |
| `orchestration/**` | 什么时候做哪一步、做完交给谁 | 阶段、进入/退出条件、诊断分支、升级与停止 | 代理权限、知识正文 |

一句话判据：

> **写在 agents 里的是「谁」，写在 knowledge 里的是「怎么想」，写在 orchestration 里的是「何时」。**

同一个句子只应有一个归属。代理提示词中出现「因为抽象名词堆叠会掩盖动作」这类内容，
应改为：`遇到抽象表达问题时读 knowledge/writing/language-abstraction.md`。

---

## 2. 文件职责

| 文件 | 归属 | 说明 |
|---|---|---|
| `prompt-architect.md` | agents | 主代理。**唯一**规定任务理解、调度、仲裁、交付的行为文件。待写 |
| `logic-analyst.md` | agents | 写作前认识与论证分析 |
| `researcher.md` | agents | 信息获取与核查 |
| `writer.md` | agents | 文本生产 |
| `evaluator.md` | agents | 写作后独立评价 |

被引用的上游文件（**不改行为，只被引用**）：

| 文件 | 提供 |
|---|---|
| `architecture/system-overview.md` | 系统定位、设计理念、代理拓扑 |
| `architecture/communication-protocol.md` | 消息类型、报告模板、分歧处理、循环控制 |
| `architecture/execution-flow.md` | 阶段划分与状态管理 |
| `orchestration/01`–`09` | 各环节工作方法 |
| `orchestration/10-writing-workflow.md` | 端到端串联（与 execution-flow 重叠，见 §4） |
| `knowledge/README.md` | 知识库结构、状态分级、检索方式 |

---

## 3. 代理提示词的统一结构

每个文件固定十节，顺序不变，每节短。缺内容就写「不适用」，不要凑字数。
主代理额外允许在第 3 节插入「不可外包的判断」，其余九节照旧。

```text
1. Identity        身份与定位（一段，说清不是谁）
2. Mission         核心任务 + 完成判据
3. Responsibilities 职责，按「当……时，做……」写
4. Workflow        调用内部步骤，带进入/退出条件
5. Knowledge       触发条件 → 文件路径（表格），不粘贴内容
6. Input           接收什么，缺什么要退回
7. Output          必须交付什么，模板只保留最小字段
8. Collaboration   与谁交互、什么必须上报
9. Authority       可自行决定 / 需用户确认 / 禁止
10. Quality Standards 可观察的验收项 + 该角色的典型失败模式
```

三条硬约束：

1. **Knowledge 节只写路径与触发条件**，不复制知识内容。
2. **Output 模板长度上限 10 个字段**，完整格式放 `communication-protocol.md`。
3. **Authority 必须出现「禁止」项**，否则代理会越界补全信息或改目标。

---

## 4. 现有重复内容与归属裁定

以下重复已确认，agents 层落地后按「保留」列收口。

| 重复内容 | 现出现位置 | 保留 | 处理 |
|---|---|---|---|
| 五个代理的职责与权限 | `system-overview.md` §3、`SKILL.md` §2（旧文件 `agent-responsibilities.md` 已删） | `agents/*.md` | 知识库与 orchestration 内 21 处已删（1,623 行）；`knowledge/README.md` §8 已删（92 行）；`system-overview.md` §3 与 `SKILL.md` §2 待收口 |
| 阶段流程（10 个 Phase） | `execution-flow.md` §3–13、`system-overview.md` §5、`SKILL.md` §4、`10-writing-workflow.md` §3–12 | 二选一（见下） | 删重叠那份，另一份补其独有内容 |
| 写作哲学（相关性≠必要性、不确定性、允许停止、不编造） | `system-overview.md` §2、`SKILL.md` §3/§7、`orchestration/README.md` §3/§10、`knowledge/philosophy/core-principles.md`、根 `README.md` §3 | `knowledge/philosophy/` | 其余改为引用 |
| 知识状态三分（Hypothesis / Validated / Operational） | `system-overview.md` §6、`SKILL.md` §5、`knowledge/README.md` §6 | `knowledge/README.md` §6 | 其余删除 |
| 消息与报告模板 | `communication-protocol.md` §4–10、`10-writing-workflow.md` §3.4/5.5/6.6/10.4、`09` §12 | `communication-protocol.md` | 其余删除 |
| 升级、询问用户、循环之外讨论 | `09-escalation…`、`08-revision…`、`execution-flow.md` §12–13、`10-writing-workflow.md` §12 | `09` 为主，`08` 只留「何时该升级」的触发判据 | 去重 |

两处需要二选一，建议如下：

- **阶段流程**：保留 `orchestration/10-writing-workflow.md`（含 Task Brief、Writer Prompt 模板，更完整），
  把 `execution-flow.md` 独有的「任务状态管理」「按复杂度自适应」两节并入，然后删除 `execution-flow.md`。
  反之亦可；**不要两份都留**。
- **主代理提示词**：`SKILL.md` 正文与 `agents/prompt-architect.md` 只能有一份是权威。
  建议 `SKILL.md` 收缩为「frontmatter + 30 行入口路由」，指向本目录。

---

## 5. 待办顺序

1. `logic-analyst.md`（已写）
2. `researcher.md`（已写）
3. `writer.md`（已写）
4. `evaluator.md`（已写）
5. `prompt-architect.md`
6. 按 §4 收口重复文件
7. 用真实任务测试：无依据的反方观点、主动发现信息缺口、抽象概念还原、Writer 中途发现认识不足、Evaluator 识别「流畅但空」的文本
