# 多Agent编排

> **章节**：专家篇第13章  
> **难度**：⭐⭐⭐⭐ 专家级  
> **阅读时间**：30-35 分钟  
> **核心收获**：掌握多Agent编排方法，构建复杂的自动化工作流

---

## 16.1 理论要点：从单Agent到多Agent

### 16.1.1 为什么需要多Agent

**单Agent的局限**：
- 上下文窗口有限
- 专业能力有限
- 并发能力有限

**多Agent的优势**：
- 专业分工：每个Agent负责特定领域
- 并发执行：多个任务同时进行
- 上下文隔离：避免上下文污染

### 16.1.2 多Agent架构模式

| 模式 | 说明 | 适用场景 |
|------|------|----------|
| **顺序执行** | Agent1 → Agent2 → Agent3 | 线性流程 |
| **并行执行** | Agent1 / Agent2 同时执行 | 独立任务 |
| **条件分支** | Agent1 → {Agent2 / Agent3} | 条件判断 |
| **循环执行** | Agent1 → Agent2 → 判断 → 回到Agent1 | 迭代优化 |
| **发布订阅** | Agent1发布事件 → Agent2/3/4订阅处理 | 事件驱动 |

### 16.1.3 Agent 角色设计

| 角色 | 职责 | 能力要求 |
|------|------|----------|
| **Planner（规划者）** | 分解任务、制定计划 | 推理能力强 |
| **Executor（执行者）** | 执行具体任务 | 工具使用熟练 |
| **Verifier（验证者）** | 检查结果、确保质量 | 判断准确 |
| **Archivist（记录者）** | 保存结果、维护记忆 | 数据管理 |

---

## 16.2 实战案例：热点选题任务的多Agent设计

### 16.2.1 场景分析

**原始单Agent流程**：
```
豆包 → 同时调用4个数据源 → 聚合 → 过滤 → 推送
```

**问题**：
- 上下文窗口有限，无法处理大量数据
- 过滤逻辑复杂，容易出错
- 无法并行调用数据源

### 16.2.2 多Agent架构

```
┌─────────────────────────────────────────┐
│              Planner Agent              │
│  - 分解任务：获取热点、过滤、格式化      │
│  - 分配子任务给Executor                  │
└─────────────────────────────────────────┘
              ↓ 分配任务
┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐
│Executor1│ │Executor2│ │Executor3│ │Executor4│
│ 微信来源 │ │ GitHub  │ │ 搜索引擎│ │ AIHOT   │
└─────────┘ └─────────┘ └─────────┘ └─────────┘
              ↓ 返回结果
┌─────────────────────────────────────────┐
│           Aggregator Agent              │
│  - 合并四个来源的结果                    │
│  - 去重                                │
└─────────────────────────────────────────┘
              ↓ 聚合结果
┌─────────────────────────────────────────┐
│           Filter Agent                  │
│  - 四维过滤（相关性/时效性/重复性/数量）│
└─────────────────────────────────────────┘
              ↓ 过滤结果
┌─────────────────────────────────────────┐
│          Formatter Agent                │
│  - 格式化为标准输出                      │
│  - 添加统计信息                          │
└─────────────────────────────────────────┘
              ↓ 格式化结果
┌─────────────────────────────────────────┐
│           Deliver Agent                 │
│  - 推送到飞书                            │
│  - 记录状态                              │
└─────────────────────────────────────────┘
```

### 16.2.3 实现代码

```python
class MultiAgentSystem:
    def __init__(self):
        self.planner = PlannerAgent()
        self.executors = {
            "wechat": WechatExecutor(),
            "github": GithubExecutor(),
            "search": SearchExecutor(),
            "aihot": AIHOTExecutor()
        }
        self.aggregator = AggregatorAgent()
        self.filter = FilterAgent()
        self.formatter = FormatterAgent()
        self.deliver = DeliverAgent()
    
    def run(self):
        # 1. 规划任务
        plan = self.planner.create_plan()
        
        # 2. 并行执行
        results = {}
        for source, executor in self.executors.items():
            results[source] = executor.execute(plan[source])
        
        # 3. 聚合
        aggregated = self.aggregator.merge(results)
        
        # 4. 过滤
        filtered = self.filter.apply(aggregated)
        
        # 5. 格式化
        formatted = self.formatter.format(filtered)
        
        # 6. 推送
        self.deliver.send(formatted)
```

---

## 16.3 Agent 间通信

### 16.3.1 通信方式

| 方式 | 说明 | 适用场景 |
|------|------|----------|
| **直接调用** | A直接调用B的方法 | 简单顺序执行 |
| **消息队列** | A发送消息到队列，B消费 | 异步解耦 |
| **事件总线** | A发布事件，B/C/D订阅 | 一对多通信 |
| **共享存储** | A写入存储，B读取 | 大数据传递 |

### 16.3.2 消息格式

```json
{
  "message_id": "uuid",
  "sender": "planner",
  "receiver": "executor_wechat",
  "timestamp": "2026-07-10T09:00:00+08:00",
  "type": "task",
  "payload": {
    "task": "fetch_hotspots",
    "params": {"date": "2026-07-10"}
  },
  "metadata": {
    "priority": "high",
    "timeout": 30
  }
}
```

---

## 16.4 图文说明

> 📌 **待补充**：建议绘制以下示意图
> 1. 多Agent架构全景图
> 2. 五种执行模式流程图
> 3. Agent角色关系图
> 4. 消息序列图

---

## 16.5 常见错误

| 错误 | 后果 | 纠正方法 |
|------|------|----------|
| Agent 职责不清 | 功能重叠或缺失 | 明确每个Agent的职责边界 |
| 过度设计 | 系统复杂难以维护 | 从单Agent开始，按需扩展 |
| 通信无协议 | 消息格式混乱 | 定义统一的消息格式 |
| 无错误隔离 | 一个Agent失败影响整体 | 每个Agent独立处理错误 |
| 上下文共享 | 信息泄露 | 最小化共享上下文 |

---

## 📝 版本迭代记录

| 版本 | 日期 | 更新内容摘要 | 操作人 |
|------|------|------------|--------|
| v1.0 | 2026-08-25 | 创建专家篇第13章 | 桂皮 |
