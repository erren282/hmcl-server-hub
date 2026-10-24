# 服务器模块

[![HMCL Plugin](https://img.shields.io/badge/HMCL-plugin-blue)](https://github.com/topics/erren282s-launcher-plugins)
[![Version](https://img.shields.io/badge/version-1.0.1-green)]()
[![License](https://img.shields.io/badge/license-GPL--3.0-lightgrey)](LICENSE)

分组页，聚合服务器相关子页面

- **插件 ID**：`org.dsh.server-hub`
- **适用启动器**：HMCL 3.16.4 定制版（需内建插件系统）
- **安装**：把 `org.dsh.server-hub_1.0.1.jar` 放入启动器目录的 `.hmcl/plugins/`，重启或在「设置 → 插件」里重新加载

> 本插件依赖 [erren282-lauuncher](https://github.com/erren282/erren282-lauuncher) 的插件系统。
> 上游 HMCL 没有该机制，无法直接装到官方版上。

---
在主页面侧边栏提供唯一的「服务器」入口，进入后以两张并排大卡片呈现已安装的服务器相关
插件页面（服务端管理、服务器列表……）。

- 汇聚内容是**动态发现**的：任何发布子页面（`PluginMenuService.registerSubPage`）的插件
  都会被这里列出，无需本插件配合修改。
- 若没有安装本插件，子页面会由启动器**直接显示为主页面侧边栏入口**，功能不受影响。
- 构建：`powershell -File build.ps1`，产物在 `dist/`。

## 窗口返回箭头

标题栏左上角的返回箭头属于启动器全局导航，默认行为是**弹出整个导航栈**——在「服务器」
模块里也会一路退回到主界面。本插件在挂载时接管这个全局动作（不修改启动器源码），按当前
所处层级分流：

| 位置 | 返回箭头的去向 |
| --- | --- |
| 子页面内部的子视图（详情 / 添加表单 / 在线目录） | 先退回该子页面的列表 |
| 子页面列表顶层 | 回到本模块的卡片选择界面 |
| 卡片选择界面 | 交还启动器：正常退出「服务器」模块回到主界面 |

实现要点：

- 接管只做一次（按 `Decorator` 记录），之后始终询问**当前挂载**的汇聚页实例；这样
  `PluginPageProxy` 每次重新进入插件页时重建内容，不会出现新旧实例互相覆盖处理器的问题。
- 子页面通过实现 `DecoratorPage` 参与分流：`back()` 返回 `false` 表示“我自己处理了”，
  返回 `true` 表示“请启动器弹栈”。这与 `DecoratorController` 的语义一致。
- `ESC` 走同一套分流：页面能处理就消费事件，处理不了再交给启动器弹栈。

回归测试：`python run_test.py`（`tools/BackRoutingTest.java`，覆盖模块内分流、真实
标题栏箭头的接管/交还、ESC 分流，共 17 条断言）。
---

## 说明：本项目为 Vibe Coding 产物

**本项目的全部代码均由 AI（大语言模型）生成**，通过对话式编程（vibe coding）完成——
人类只提出需求、给出反馈与验收，不手写实现代码。

- 代码可能包含未经验证的边界情况、不一致的风格，或与常规工程实践不同的设计取舍
- 请在生产环境使用前自行审查、测试与评估
- 欢迎提交 issue 或 PR 改进
