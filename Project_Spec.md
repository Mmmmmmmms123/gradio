# Timeline 时间线组件 - 需求规格文档

## 1. 需求概述

### 1.1 核心目标
为 Gradio 添加一个 Timeline（时间线）组件，用于可视化展示事件的时间顺序和状态变化。

### 1.2 使用场景
- 展示机器学习模型的训练历史
- 展示实验记录和结果演进
- 展示数据处理 pipeline 的执行步骤
- 展示项目里程碑和进度

### 1.3 设计原则
- **第一性原理**：时间线的本质是"按时间顺序展示事件及其状态"
- **最小可用**：先实现核心展示功能，再逐步扩展交互能力
- **Gradio 一致性**：遵循 Gradio 现有组件的设计模式和 API 风格

---

## 2. 功能规格

### 2.1 核心功能

#### 2.1.1 数据展示
- 支持展示多个时间节点/事件
- 每个事件包含：
  - `title`: 事件标题（必填）
  - `description`: 事件描述（可选）
  - `timestamp`: 时间戳（可选，默认按顺序展示）
  - `status`: 状态（可选，支持：completed, in-progress, pending, error）
  - `icon`: 自定义图标（可选）
  - `color`: 自定义颜色（可选）

#### 2.1.2 布局模式
- **垂直模式**（默认）：从上到下展示
- **水平模式**：从左到右展示

#### 2.1.3 状态指示
- `completed`: 已完成（绿色 ✓）
- `in-progress`: 进行中（蓝色 ⟳）
- `pending`: 待处理（灰色 ○）
- `error`: 错误（红色 ✗）

### 2.2 交互功能
- 点击事件节点可触发回调
- 支持悬停显示详细信息
- 支持动态更新数据（Gradio 的 live 模式）

### 2.3 样式定制
- 支持自定义主题色
- 支持自定义节点大小
- 支持自定义连线样式

---

## 3. API 设计

### 3.1 Python API

```python
import gradio as gr

# 基础用法
timeline = gr.Timeline(
    value=[
        {"title": "开始训练", "timestamp": "2024-01-01 10:00", "status": "completed"},
        {"title": "Epoch 10", "timestamp": "2024-01-01 10:30", "status": "completed"},
        {"title": "Epoch 20", "timestamp": "2024-01-01 11:00", "status": "in-progress"},
        {"title": "评估模型", "timestamp": "2024-01-01 11:30", "status": "pending"},
    ],
    layout="vertical",  # "vertical" | "horizontal"
    label="训练进度",
)
```

### 3.2 参数说明

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `value` | List[Dict] | `[]` | 时间线事件列表 |
| `layout` | str | `"vertical"` | 布局模式 |
| `label` | str | `""` | 组件标签 |
| `show_label` | bool | `True` | 是否显示标签 |
| `visible` | bool | `True` | 是否可见 |
| `interactive` | bool | `True` | 是否可交互 |

### 3.3 事件数据结构

```python
{
    "title": str,           # 必填，事件标题
    "description": str,     # 可选，事件描述
    "timestamp": str,       # 可选，时间戳
    "status": str,          # 可选，状态
    "icon": str,            # 可选，图标
    "color": str,           # 可选，颜色
}
```

---

## 4. 技术实现

### 4.1 文件结构

```
gradio/
├── components/
│   └── timeline.py         # Python 后端逻辑
└── ...

js/
├── timeline/
│   ├── Index.svelte        # 主组件
│   ├── TimelineItem.svelte # 单个事件节点
│   ├── TimelineConnector.svelte # 连接线
│   ├── package.json        # 包配置
│   └── ...
└── ...
```

### 4.2 Python 后端 (`gradio/components/timeline.py`)

- 继承 `gradio.components.Component`
- 定义数据序列化方法（`preprocess`, `postprocess`）
- 定义组件配置和示例

### 4.3 前端组件 (`js/timeline/`)

- 使用 Svelte 框架（与 Gradio 其他组件一致）
- 实现垂直/水平布局
- 实现状态指示器
- 实现交互事件处理

### 4.4 样式
- 使用 CSS 变量支持主题定制
- 遵循 Gradio 的设计系统
- 支持响应式布局

---

## 5. 测试策略

### 5.1 单元测试
- Python 后端数据序列化测试
- 前端组件渲染测试

### 5.2 集成测试
- 组件在 Gradio App 中的完整流程测试
- 交互功能测试

### 5.3 示例应用
- 创建示例展示组件用法
- 验证与 Gradio 其他组件的兼容性

---

## 6. 验收标准

- [ ] 组件可以在 Gradio App 中正常渲染
- [ ] 支持垂直和水平两种布局模式
- [ ] 支持 4 种状态显示
- [ ] 支持动态数据更新
- [ ] 支持点击事件回调
- [ ] 通过 Gradio 的测试框架
- [ ] 有完整的文档和示例

---

## 7. 开发优先级

### P0（必须实现）
- 基础数据展示
- 垂直布局
- 4 种状态指示

### P1（应该实现）
- 水平布局
- 点击事件回调
- 动态数据更新

### P2（可以后续添加）
- 自定义图标
- 自定义颜色
- 悬停详情

---

## 8. 参考资源

- Gradio 组件开发指南：https://github.com/gradio-app/gradio/blob/main/CONTRIBUTING.md
- Gradio 现有组件实现参考：`/gradio/components/` 目录
- Svelte 组件文档：https://svelte.dev/docs
