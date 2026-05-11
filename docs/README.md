# Gradio Timeline 组件开发引导

## 快速开始

### 1. 环境准备

#### 系统要求
- Python 3.10+
- Node.js 16.14+
- pnpm 9.x

#### 安装步骤
```bash
# 1. Fork 并 Clone 仓库
git clone https://github.com/YOUR_USERNAME/gradio.git
cd gradio

# 2. 安装 Python 依赖
pip install -e .

# 3. 安装前端依赖
pnpm install

# 4. 运行开发服务器
gradio app.py  # 或 python app.py
```

---

## 2. 项目结构

### 2.1 核心目录
```
gradio/
├── components/           # Python 组件定义
│   ├── component.py      # 基类
│   ├── button.py         # 参考实现
│   └── timeline.py       # 我们的组件
│
js/
├── timeline/             # 前端组件
│   ├── Index.svelte      # 主入口
│   ├── package.json      # 包配置
│   └── ...
│
test/
└── components/           # Python 测试
    └── test_timeline.py
```

### 2.2 关键文件说明

#### Python 端 (`gradio/components/timeline.py`)
- 继承 `Component` 基类
- 实现 `preprocess()`: 前端 → 后端数据转换
- 实现 `postprocess()`: 后端 → 前端数据转换
- 定义组件配置和示例

#### 前端端 (`js/timeline/`)
- `Index.svelte`: 主组件，处理布局和渲染
- 使用 Svelte 语法
- 遵循 Gradio 设计系统

---

## 3. 开发步骤

### Step 1: Python 后端

创建 `gradio/components/timeline.py`:

```python
from __future__ import annotations
from typing import Any, List, Dict, Optional
from gradio.components import Component

class Timeline(Component):
    """
    Timeline component for displaying events in chronological order.
    """
    
    def __init__(
        self,
        value: List[Dict[str, Any]] | None = None,
        layout: str = "vertical",
        label: str = "",
        show_label: bool = True,
        visible: bool = True,
        interactive: bool = True,
    ):
        # 实现初始化逻辑
        pass
    
    def preprocess(self, payload: List[Dict] | None) -> List[Dict] | None:
        # 前端 → 后端数据转换
        pass
    
    def postprocess(self, value: List[Dict] | None) -> List[Dict] | None:
        # 后端 → 前端数据转换
        pass
    
    # 其他必要的方法...
```

**参考文件**：
- `gradio/components/button.py`
- `gradio/components/component.py`

### Step 2: 前端组件

创建 `js/timeline/Index.svelte`:

```svelte
<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  
  export let value: Array<any> = [];
  export let layout: string = 'vertical';
  
  const dispatch = createEventDispatcher();
</script>

<div class="timeline timeline-{layout}">
  {#each value as event, index}
    <div class="timeline-item">
      <!-- 渲染事件内容 -->
    </div>
  {/each}
</div>

<style>
  .timeline {
    /* 样式实现 */
  }
</style>
```

**参考文件**：
- `js/button/Index.svelte`
- `js/markdown/Index.svelte`

### Step 3: 注册组件

在 `gradio/components/__init__.py` 中导出 Timeline：
```python
from gradio.components.timeline import Timeline
```

在 `js/index.ts` 中注册前端组件。

### Step 4: 测试

创建 `test/components/test_timeline.py`:
```python
import pytest
from gradio.components import Timeline

def test_timeline_init():
    timeline = Timeline()
    assert timeline.value == []

def test_timeline_postprocess():
    # 测试数据处理逻辑
    pass
```

### Step 5: 示例应用

创建 `demo/timeline_demo.py`:
```python
import gradio as gr

with gr.Blocks() as demo:
    timeline = gr.Timeline(
        value=[
            {"title": "事件1", "status": "completed"},
            {"title": "事件2", "status": "in-progress"},
        ]
    )

demo.launch()
```

---

## 4. 调试技巧

### 4.1 本地开发
```bash
# 运行后端（自动重载）
gradio app.py

# 运行前端开发服务器
pnpm run dev
```

### 4.2 常见问题

**Q: 组件不渲染？**
- 检查是否在 `__init__.py` 中导出
- 检查前端是否正确注册
- 查看浏览器控制台错误

**Q: 数据传递不正确？**
- 检查 `preprocess` 和 `postprocess` 实现
- 使用 `print` 或 `console.log` 调试数据流

**Q: 样式不生效？**
- 检查 CSS 变量是否使用正确
- 检查样式是否被覆盖

---

## 5. 提交前检查

### 5.1 代码质量
```bash
# Python 代码检查
ruff check gradio/components/timeline.py

# 前端代码检查
pnpm run lint
```

### 5.2 测试
```bash
# 运行测试
pytest test/components/test_timeline.py
```

### 5.3 构建
```bash
# 构建前端
pnpm run build
```

---

## 6. 参考资源

- Gradio 贡献指南：https://github.com/gradio-app/gradio/blob/main/CONTRIBUTING.md
- Gradio 组件开发视频：见 CONTRIBUTING.md
- Svelte 文档：https://svelte.dev/docs
- Gradio 组件源码：`gradio/components/` 目录

---

## 7. 时间线组件特有注意事项

### 7.1 数据格式
必须严格遵循 `Project_Spec_0323.md` 定义的事件结构：
```python
{
    "title": str,           # 必填
    "description": str,     # 可选
    "timestamp": str,       # 可选
    "status": str,          # 可选：completed, in-progress, pending, error
}
```

### 7.2 状态颜色
使用 Gradio 设计系统的 CSS 变量：
- completed: `--gradio-color-success`
- in-progress: `--gradio-color-primary`
- pending: `--gradio-color-neutral`
- error: `--gradio-color-error`

### 7.3 布局实现
- 垂直：使用 flex-direction: column
- 水平：使用 flex-direction: row
- 连接线使用伪元素或单独的元素实现
