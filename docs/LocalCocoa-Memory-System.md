# Local Cocoa Memory System - 技术演进文档

**版本: Memory v2.5** | **更新时间: 2026年2月1日**

---

## 📌 核心理念

Memory 系统是 Local Cocoa 的核心模块，旨在将用户的文件和对话转化为结构化的语义记忆。

### 设计目标

- 从原始数据中提取事件、故事、事实、计划
- 构建用户画像，实现个性化理解
- 支持长期记忆的积累与检索

---

## 📈 版本演进总览

| 版本 | 日期 | 主要特性 |
|------|------|----------|
| Memory v1.0 | 2025.11.26 | 基础记忆提取 |
| Memory v1.5 | 2025.12.20 | 分块提取 + 断点续传 |
| Memory v1.6 | 2026.1.5 | 配置管理 + 数据迁移 |
| Memory v2.0 | 2026.1.13 | MemCell 架构引入 |
| Memory v2.2 | 2026.01.18 | LLM 用户画像推理 |
| **Memory v2.5** | **2026.02.01** | **Email 记忆快速打标 + QA ← 当前版本** |

---

## 🔄 Memory v1.0 - 基础记忆提取

### 核心功能 - 四种记忆类型

1. **Episode Memory** - 情节记忆，叙事性事件总结
2. **Event Log** - 事件日志，原子事实记录
3. **Foresight** - 前瞻性记忆，未来计划和预测
4. **Profile** - 用户画像，个性化特征

---

## ✨ Memory v1.5 - 分块提取增强

### 核心改进

- ✓ **分块提取机制** - 支持将文件分成多个 chunk，逐块提取
- ✓ **进度追踪** - 记录每个文件的提取进度
- ✓ **断点续传** - 支持暂停和恢复，避免重复提取
- ✓ **手动触发** - 用户可自主选择提取时机

---

## ⚙️ Memory v1.6 - 配置管理与数据迁移

### 核心改进

- ✓ **数据目录重构**: `local_rag` → `synvo_db`
- ✓ **自动迁移机制** - 自动检测并迁移旧数据
- ✓ **配置面板** - 可配置提取阶段、参数调整

---

## 🎯 Memory v2.0 - MemCell 架构引入

v2.0 的核心创新是引入 **MemCell** 概念，作为原始数据和记忆提取之间的中间抽象层。

### MemCell 的作用

- ✓ 规范化不同来源的数据（对话、文档）
- ✓ 通过 LLM 驱动的边界检测，智能分割数据
- ✓ 为每个数据单元提供完整的元数据和上下文
- ✓ 支持数据的可追溯性和版本管理

### 架构图

```
原始数据 → MemCell Extractor → MemCell → Memory Extractor → Episode/EventLog/Foresight
                  ↓
           边界检测 + 元数据提取
```

---

## 🔧 Memory v2.1 - 框架整合与完善

### 核心改进

- ✓ **MemCell 提取器完整实现** - 对话和文档双支持
- ✓ **LLM 驱动的智能边界检测**
- ✓ **完整的 API 路由与 Service 层设计**
- ✓ **前端 UI 组件全面扩展** (`UserMemory.tsx` - 661 行)

---

## 🤖 Memory v2.2 - LLM 用户画像推理

### 系统信息驱动的用户画像推理

1. **Step 1: 系统数据收集** - 收集已安装应用、开发工具、系统配置、文件类型分布
2. **Step 2: LLM 推理分析** - 系统信息 → LLM → 用户特征推断
3. **Step 3: 渐进式输出** - 逐步输出基本信息、技术画像、工作生涯、兴趣爱好、心理画像、行为模式

### 核心功能

- ✓ 系统信息收集（`system_profile.py`）
- ✓ LLM 驱动的画像推理
- ✓ 流式输出（SSE）支持
- ✓ 分层主题结构（`ProfileTopic` / `ProfileSubtopic`）

---

## 📧 Memory v2.5 - Email 记忆系统（当前版本）

v2.5 将 Memory 系统与 Email 插件深度整合，支持批量邮件记忆构建、可视化展示和智能问答。

### 核心功能

#### 1. 批量邮件记忆构建（Build Memory）

支持账户级别的邮件记忆批量构建：

- ✓ **MemCell 创建** - 每封邮件转换为一个 MemCell（原始数据容器）
- ✓ **Episode 提取** - LLM 从邮件内容提取情节摘要
- ✓ **Fact 提取** - LLM 从 Episode 提取原子事实（EventLog）
- ✓ **流式进度** - 实时 SSE 流显示构建进度

#### 2. 增量构建与去重

避免重复处理已有邮件：

- ✓ **Build New** - 只处理尚未构建记忆的邮件（跳过已存在）
- ✓ **Force Rebuild All** - 强制重新处理所有邮件
- ✓ **稳定 ID 生成** - 使用 SHA256 哈希生成稳定的 MemCell/Episode/Fact ID
- ✓ **Upsert 机制** - 防止多次构建产生重复数据

#### 3. 失败处理与重试

追踪失败邮件并支持单独重试：

- ✓ **状态追踪** - 每封邮件记录 `memory_status`: pending/success/failed
- ✓ **错误记录** - 保存失败原因用于排查
- ✓ **单独重试** - 可针对单封失败邮件重新构建

#### 4. 记忆可视化面板

新增 `EmailQAPage.tsx` 组件展示构建结果：

- ✓ **MemCells 列表**（紫色）- 显示邮件主题、发件人、预览
- ✓ **Episodes 列表**（蓝色）- 显示情节摘要和叙事
- ✓ **Facts 列表**（琥珀色）- 显示原子事实

#### 5. 邮件 QA 问答

基于邮件记忆进行智能问答：

- ✓ **账户级问答** - 基于整个邮箱的记忆回答问题
- ✓ **记忆检索** - 自动检索相关的历史记忆作为上下文
- ✓ **流式输出** - 支持 SSE 流式返回 LLM 回答

#### 6. Memory Overview 重设计

优化 `UserMemory.tsx` 的概览页面：

- ✓ **Identity Card** - 在概览页直接展示关键用户画像信息
- ✓ **精简布局** - 移除冗余的 Recent Events/Episodes/Foresights 区块

### 技术实现

#### 后端 API（`plugins/mail/backend/`）

| 端点 | 方法 | 功能 |
|------|------|------|
| `/accounts/{id}/build-memory/stream` | POST | 流式构建邮件记忆 |
| `/accounts/{id}/memory-status` | GET | 获取记忆构建状态 |
| `/accounts/{id}/memory-details` | GET | 获取 MemCells/Episodes/Facts 详情 |
| `/accounts/{id}/failed-emails` | GET | 获取失败邮件列表 |
| `/accounts/{id}/retry-email/{msg_id}` | POST | 重试单封失败邮件 |
| `/accounts/{id}/qa` | POST | 账户级邮件问答 |

#### 数据库 Schema 更新

`email_messages` 表新增字段：
- `memory_status TEXT` - 'pending' | 'success' | 'failed'
- `memory_error TEXT` - 失败错误信息
- `memory_built_at TEXT` - 最后构建时间

#### Memory ID 生成策略

使用哈希确保 ID 稳定，支持 upsert：

```python
memcell_id = SHA256(chunk_id)[:32]
episode_id = SHA256(memcell_id + "_episode")[:32]
event_log_id = SHA256(episode_id + "_fact_" + index)[:32]
```

#### 数据流

```
邮箱账户 → 遍历邮件 → MemCell 创建 → Episode 提取 → Fact 提取
                           ↓
                    存储到 memory_memcells / memory_episodes / memory_event_logs
                           ↓
                    更新 email_messages.memory_status
```

### 新增/修改文件

```
plugins/mail/backend/
├── router.py      # +275 行: 新增 build-memory/memory-details/retry 等端点
└── service.py     # +1003 行: 批量构建、重试、QA 逻辑

src/renderer/components/
├── EmailQAPage.tsx      # +1251 行: 新增记忆可视化和问答页面
├── UserMemory.tsx       # 修改: Identity Card 集成到 Overview
└── ExtensionsView.tsx   # 修改: 集成 EmailQAPage

services/storage/
├── memory.py      # +123 行: 新增 get_memcells_by_group_id / delete_event_logs_by_episode
└── emails.py      # +62 行: 新增 memory_status 追踪方法

src/main/
├── backendClient.ts     # +179 行: 新增 API 客户端函数
└── ipc/email.ts         # +57 行: 新增 IPC handlers
```

### UI 预览

```
┌─────────────────────────────────────────────────────────────────┐
│ 📧 Email Memory Overview                                        │
│                                                                 │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐                │
│ │ 📧 100      │ │ 📄 7        │ │ ✨ 47       │                │
│ │ MemCells    │ │ Episodes    │ │ Facts       │                │
│ └─────────────┘ └─────────────┘ └─────────────┘                │
│                                                                 │
│ ┌─────────────────────────────────────────────────────────────┐│
│ │ 💜 MemCells (100)                                           ││
│ │ ├─ 📧 Meeting Request - john@example.com                    ││
│ │ ├─ 📧 Project Update - team@company.com                     ││
│ │ └─ ...                                                      ││
│ └─────────────────────────────────────────────────────────────┘│
│                                                                 │
│ ┌─────────────────────────────────────────────────────────────┐│
│ │ 💙 Episodes (7)                                             ││
│ │ ├─ 📄 Weekly meeting scheduled for Monday 10am              ││
│ │ └─ ...                                                      ││
│ └─────────────────────────────────────────────────────────────┘│
│                                                                 │
│ ┌─────────────────────────────────────────────────────────────┐│
│ │ 🧡 Atomic Facts (47)                                        ││
│ │ ├─ ✨ John is the project manager                           ││
│ │ ├─ ✨ Meeting room is Conference Room A                     ││
│ │ └─ ...                                                      ││
│ └─────────────────────────────────────────────────────────────┘│
│                                                                 │
│ [🔨 Build New]  [🔄 Force Rebuild All]                         │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📊 版本对比表

| 功能 | v1.0 | v1.5 | v1.6 | v2.0 | v2.2 | v2.5 |
|------|------|------|------|------|------|------|
| 基础记忆提取 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| 分块提取 | | ✓ | ✓ | ✓ | ✓ | ✓ |
| 断点续传 | | ✓ | ✓ | ✓ | ✓ | ✓ |
| 配置管理 | | | ✓ | ✓ | ✓ | ✓ |
| MemCell 架构 | | | | ✓ | ✓ | ✓ |
| LLM 用户画像 | | | | | ✓ | ✓ |
| Email 批量记忆构建 | | | | | | ✓ |
| Email 记忆可视化 | | | | | | ✓ |
| 增量构建/去重 | | | | | | ✓ |
| 失败重试机制 | | | | | | ✓ |
| Email QA 问答 | | | | | | ✓ |
| Memory Overview 重设计 | | | | | | ✓ |

---

## 📚 核心文件索引

### Memory 核心模块

```
services/memory/
├── router.py                 # API 路由
├── service.py                # 服务层
├── models.py                 # Pydantic 模型
├── system_profile.py         # 系统画像收集
│
├── memory_layer/
│   ├── memory_manager.py     # 记忆管理器
│   ├── memcell_extractor/    # MemCell 提取器
│   ├── memory_extractor/     # 记忆提取器
│   └── llm/                  # LLM 提供者
│
└── api_specs/
    ├── memory_types.py       # 核心类型定义
    └── memory_models.py      # 数据模型
```

### Email 记忆集成

```
plugins/mail/backend/
├── router.py    # build-memory/memory-details/retry/qa 端点
└── service.py   # 批量构建、重试、QA 服务方法

src/renderer/components/
├── EmailQAPage.tsx        # 记忆可视化和问答页面
├── EmailBrowser.tsx       # 邮件浏览器
├── EmailConnectorsPanel.tsx  # 邮箱连接面板
└── UserMemory.tsx         # 用户记忆概览（含 Identity Card）

services/storage/
├── memory.py    # 记忆 CRUD 操作
└── emails.py    # 邮件状态追踪
```

---

## 📝 更新日志

### [v2.5] - 2026-02-01 Email 记忆系统 ← 当前版本

**新功能：**
- ✓ **批量邮件记忆构建** - 账户级别的 MemCell → Episode → Fact 提取
- ✓ **记忆可视化面板** - 分别展示 MemCells（紫）、Episodes（蓝）、Facts（琥珀）
- ✓ **增量构建** - "Build New" 跳过已处理邮件，避免重复
- ✓ **Force Rebuild** - 强制重建所有邮件记忆
- ✓ **失败重试** - 追踪失败邮件并支持单独重试
- ✓ **邮件 QA 问答** - 基于账户记忆进行智能问答
- ✓ **Memory Overview 重设计** - Identity Card 集成到概览页

**技术改进：**
- ✓ 新增 6 个后端 API 端点（build-memory/memory-details/failed-emails/retry-email 等）
- ✓ 新增 `EmailQAPage.tsx` 组件（+1251 行）
- ✓ 使用 SHA256 哈希生成稳定 ID，支持 upsert 操作
- ✓ 数据库新增 `memory_status/memory_error/memory_built_at` 字段
- ✓ SSE 流式进度更新
- ✓ 扩展 Storage 层方法（memory.py +123 行，emails.py +62 行）

---

### [v2.2] - 2026-01-18 LLM 用户画像推理

- ✓ LLM 驱动的用户画像推理系统
- ✓ 系统信息收集与分析
- ✓ 渐进式画像生成（SSE）

---

### [v2.0] - 2026-01-13 MemCell 架构

- ✓ MemCell 核心架构引入
- ✓ Router 和 Service 层更新
- ✓ UserMemory UI 组件扩展
- ✓ Storage 层重写

---

### [v1.6] - 2026-01-05 配置管理

- ✓ 数据目录重命名
- ✓ 自动迁移机制
- ✓ 配置管理面板

---

### [v1.5] - 2025-12-20 分块提取

- ✓ 分块提取机制
- ✓ 进度追踪与断点续传
- ✓ 手动触发选项

---

### [v1.0] - 2025-11-26 初始版本

- ✓ 基础记忆提取系统
- ✓ 四种记忆类型支持
- ✓ 四层模块化设计

---

## 🔮 未来规划

### v2.6 计划

- [ ] 完整 MemCell Extractor 流程集成（使用 LLM 提取 summary/keywords）
- [ ] 邮件记忆向量化与语义检索
- [ ] ~~批量邮件记忆提取~~ ✅ 已在 v2.5 实现
- [ ] ~~记忆可视化面板~~ ✅ 已在 v2.5 实现
- [ ] 记忆关联图谱（跨邮件的实体关联）
- [ ] 配置云端 LLM（OpenRouter/Deepseek）支持

### v3.0 愿景

- [ ] 多模态记忆（图片、音频）
- [ ] 记忆图谱可视化
- [ ] 跨设备记忆同步
- [ ] 隐私保护增强
- [ ] 日历/联系人记忆集成
