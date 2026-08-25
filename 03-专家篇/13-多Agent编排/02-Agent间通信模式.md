# Agent 间通信模式

> **章节**：专家篇第13章第2节  
> **难度**：⭐⭐⭐⭐ 专家级  
> **阅读时间**：25-30 分钟  
> **核心收获**：掌握多Agent间的通信方式和协议设计

---

## 16.6 理论要点：Agent通信的本质

### 16.6.1 为什么需要通信协议

**问题**：多个Agent独立运行，如何协调工作？

**通信协议的价值**：
- 标准化消息格式
- 明确职责边界
- 支持异步解耦
- 便于监控和调试

### 16.6.2 通信模式分类

| 模式 | 说明 | 适用场景 | 复杂度 |
|------|------|----------|--------|
| **直接调用** | A直接调用B | 简单顺序执行 | 低 |
| **消息队列** | A发送消息，B消费 | 异步解耦 | 中 |
| **事件总线** | A发布事件，多消费者 | 一对多通信 | 中 |
| **共享存储** | A写入，B读取 | 大数据传递 | 中 |
| **API网关** | 统一入口，路由分发 | 复杂系统 | 高 |

---

## 16.7 实战案例：消息队列实现

### 16.7.1 场景：热点选题任务的消息流

```
Planner → Executor1: {task: "fetch_wechat"}
Planner → Executor2: {task: "fetch_github"}
Planner → Executor3: {task: "fetch_search"}
Planner → Executor4: {task: "fetch_aihot"}

Executor1 → Aggregator: {source: "wechat", data: [...]}
Executor2 → Aggregator: {source: "github", data: [...]}
Executor3 → Aggregator: {source: "search", data: [...]}
Executor4 → Aggregator: {source: "aihot", data: [...]}

Aggregator → Filter: {items: [...], sources: {...}}
Filter → Formatter: {filtered_items: [...]}
Formatter → Deliver: {formatted_output: "..."}
```

### 16.7.2 消息格式设计

```json
{
  "message_id": "uuid",
  "correlation_id": "batch-uuid",
  "sender": "planner",
  "receiver": "executor_wechat",
  "timestamp": "2026-07-10T09:00:00+08:00",
  "type": "task",
  "priority": "high",
  "payload": {
    "task": "fetch_hotspots",
    "params": {
      "date": "2026-07-10",
      "max_items": 50
    }
  },
  "metadata": {
    "timeout": 30,
    "retry_count": 0,
    "max_retries": 2
  }
}
```

### 16.7.3 消息队列实现

```python
import asyncio
from collections import deque

class MessageQueue:
    def __init__(self):
        self.queues = {}
        self.handlers = {}
    
    def register(self, agent_id, handler):
        """注册Agent和处理器"""
        self.queues[agent_id] = deque()
        self.handlers[agent_id] = handler
    
    async def send(self, to, message):
        """发送消息"""
        if to in self.queues:
            self.queues[to].append(message)
            return True
        return False
    
    async def process(self, agent_id):
        """处理消息"""
        while self.queues[agent_id]:
            message = self.queues[agent_id].popleft()
            await self.handlers[agent_id](message)
```

---

## 16.8 图文说明

> 📌 **待补充**：建议绘制以下示意图
> 1. 五种通信模式对比图
> 2. 消息序列图
> 3. 消息队列架构图
> 4. 错误处理流程图

---

## 16.9 常见错误

| 错误 | 后果 | 纠正方法 |
|------|------|----------|
| 通信无协议 | 消息格式混乱 | 定义统一的消息格式 |
| 同步阻塞 | 性能低下 | 使用异步通信 |
| 无错误处理 | 消息丢失 | 实现重试和死信队列 |
| 过度通信 | 网络开销大 | 合并消息，减少通信次数 |
| 无监控 | 无法排查问题 | 记录所有消息和状态 |

---

## 📝 版本迭代记录

| 版本 | 日期 | 更新内容摘要 | 操作人 |
|------|------|------------|--------|
| v1.0 | 2026-08-25 | 创建专家篇第13章第2节 | 桂皮 |
