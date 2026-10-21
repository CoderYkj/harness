# 30 - 场景与资源规范

## 场景

- 可复用 UI 组件必须独立场景（如 `scenes/ui/primary_button.tscn`）。
- 整屏场景放 `scenes/screens/`。
- 保持节点树浅而清晰，避免深层嵌套。
- 使用 **Scene Unique Name**（`%Name`）标记需要在脚本中访问的关键节点，减少对路径的依赖。
- 场景与脚本同构命名：`settings_panel.tscn` ↔ `settings_panel.gd`。

## 资源

- 数据驱动使用 `Resource` + `.tres`，**不要为每份数据复制脚本**。
- 高频 `@export Resource` 变量勾选 **Local to Scene**，或在 `_ready()` 中 `duplicate()`，避免共享状态。
- 运行时不要直接修改 `.tres` 磁盘文件；先 `duplicate()` 再改。
- 主题、字体、样式统一放 `resources/themes/`，不要在场景中散落硬编码颜色和字号。
- 高频率查找用 `Dictionary` + `StringName` 键，避免用 `Array` 线性搜索。

## 加载

- 大场景/大量资源不要在主线程同步 `load()`，使用 `ResourceLoader.load_threaded_request` 或预加载策略。
- 高频实例化（列表项、子弹、提示）考虑对象池，不要每帧 `instantiate()` 上百个节点。

## UI 主题

- 优先使用 `Theme` 资源统一管理颜色、字体、样式。
- 不要在多个场景中重复定义同一套 StyleBox；提取到 `resources/themes/`。
- Agent 修改主题时，必须同时检查所有引用该主题的场景是否能正常加载。