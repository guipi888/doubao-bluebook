# Prompt 版本管理工具

> **章节**：专家篇第15章第3节  
> **难度**：⭐⭐⭐⭐ 专家级  
> **阅读时间**：20-25 分钟  
> **核心收获**：掌握 Prompt 版本管理的工具和实践

---

## 18.10 理论要点：工具化的重要性

### 18.10.1 为什么需要工具

**手动管理的痛点**：
- 版本号容易混乱
- 变更记录不完整
- 对比差异困难
- 回滚操作复杂

**工具化的价值**：
- 自动化版本管理
- 完整的变更历史
- 快速对比和回滚
- 团队协作支持

### 18.10.2 工具选型

| 工具 | 适用场景 | 优点 | 缺点 |
|------|----------|------|------|
| **Git** | 文本文件版本管理 | 成熟、强大 | 需要 Git 知识 |
| **版本控制平台** | 团队协作 | UI 友好、协作功能 | 需要网络 |
| **自定义脚本** | 简单需求 | 灵活、轻量 | 需要开发 |
| **专用工具** | 复杂需求 | 功能完善 | 学习成本 |

---

## 18.11 实战案例：使用 Git 管理 Prompt

### 18.11.1 目录结构

```
prompts/
├── ai-hotspot/
│   ├── README.md
│   ├── current.md          # 当前版本（软链接）
│   ├── v1.0.md
│   ├── v1.1.md
│   ├── v1.2.md
│   └── changelog.md
├── title-generator/
│   └── ...
└── .gitignore
```

### 18.11.2 Git 工作流

```bash
# 1. 克隆仓库
git clone git@github.com:team/prompts.git
cd prompts

# 2. 创建新版本
cp ai-hotspot/current.md ai-hotspot/v1.3.md
# 编辑 v1.3.md

# 3. 提交变更
git add ai-hotspot/v1.3.md ai-hotspot/changelog.md
git commit -m "feat: v1.3 - 增加排除旧话题过滤"

# 4. 更新当前版本
ln -sf v1.3.md ai-hotspot/current.md
git add ai-hotspot/current.md
git commit -m "chore: 切换到 v1.3"

# 5. 推送
git push
```

### 18.11.3 对比工具

```bash
# 对比两个版本
git diff v1.2 v1.3 -- prompts/ai-hotspot/

# 查看变更历史
git log --oneline -- prompts/ai-hotspot/

# 回滚到指定版本
git checkout v1.2 -- prompts/ai-hotspot/current.md
```

---

## 18.12 图文说明

> 📌 **待补充**：建议绘制以下示意图
> 1. Git 工作流程图
> 2. 版本对比图
> 3. 分支策略图
> 4. 工具对比表

---

## 18.13 常见错误

| 错误 | 后果 | 纠正方法 |
|------|------|----------|
| 无版本管理 | 改坏了无法回滚 | 使用 Git |
| 变更无记录 | 不知道改了什么 | 维护 changelog |
| 直接改 current | 历史丢失 | 创建新版本，再切换 |
| 不 review | 错误被提交 | Code Review |
| 大文件入库 | 仓库臃肿 | 忽略大文件 |

---

## 📝 版本迭代记录

| 版本 | 日期 | 更新内容摘要 | 操作人 |
|------|------|------------|--------|
| v1.0 | 2026-08-25 | 创建专家篇第15章第3节 | 桂皮 |
