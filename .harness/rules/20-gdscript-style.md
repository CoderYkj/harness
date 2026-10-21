# 20 - GDScript 编码规范

## 命名

- 文件夹与 `.gd` 文件：`snake_case`（官方推荐，避免 Windows 导出大小写问题）。
- 节点名：`PascalCase`。
- 类名 / `class_name`：`PascalCase`。
- 函数与变量：`snake_case`。
- 常量：`UPPER_SNAKE_CASE`。
- 信号：`snake_case`，过去式或事件式，如 `panel_closed`。

## 类型

- 优先静态类型：
  ```gdscript
  var health: int = 100
  var target: Node2D
  func update_label(text: String) -> void:
  ```
- 能用 `:=` 推断时用 `:=`。
- 避免无类型声明；Agent 不应新增 `untyped-declaration` 警告。

## 节点引用

- 优先 `@onready var label: Label = %TitleLabel`。
- 使用 **Scene Unique Name**（`%Name`），不要用绝对路径 `get_node("/root/.../...")`。
- 不要在高频函数 `_process` / `_physics_process` 中反复 `get_node()`；在 `@onready` 中缓存。

## 帧与物理

- 移动、物理相关逻辑使用 `_physics_process`，不要用 `_process` 做移动。
- 使用 `move_and_slide()` 时，**不要再手动乘 `delta`**，否则双重缩放。
- 纯 UI 动画/渐变可以用 `_process`，但必须显式乘 `delta`。

## 信号

- UI 与逻辑通过信号解耦，不要让 HUD 直接引用 Player。
- 长期存活的信号连接注意断开，或使用 `CONNECT_ONE_SHOT`。
- 不在信号总线中传递 `Node` 引用，优先传 ID 或数据。

## 其他

- 不硬编码动画名、节点路径、资源路径；用常量或映射。
- 不吞错误；必要时 `push_error` / `push_warning`。
- 删除调试 `print` 再提交。
- 公共函数写返回类型；复杂逻辑写简短注释解释“为什么”。