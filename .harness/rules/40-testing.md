# 40 - 测试（GUT）

## 框架选择

- 纯 GDScript 前端项目：**GUT**（Godot Unit Testing）。
- 如果未来引入 C# 或需要更丰富的场景运行器，再考虑 gdUnit4；当前不混用。

## 原则

- 新 UI 组件必须有测试：节点存在、信号发出、状态变化、边界输入。
- Bug 修复先写失败测试，再修复。
- 测试可重复、无真实时间依赖、无外部网络。
- 测试节点用 `add_child_autofree` 确保释放。

## 覆盖要求

- `scripts/ui/` 中每个可复用组件的公开方法/信号至少 1 个测试。
- `scripts/screens/` 核心状态变化和按钮触发有测试。
- 修改信号连接、资源加载逻辑必须补测试。

## 结构

```gdscript
extends GutTest

func test_primary_button_emits_pressed_signal() -> void:
    var button := preload("res://scenes/ui/primary_button.tscn").instantiate()
    add_child_autofree(button)
    # act
    button.pressed.emit()
    # assert
    assert_true(button.is_pressed, "button should be pressed")
```

## 运行

```bash
godot --headless -s addons/gut/gut_cmdln.gd -gdir=res://tests -gexit
```

单文件：

```bash
godot --headless -s addons/gut/gut_cmdln.gd -gtest=res://tests/unit/test_xxx.gd -gexit
```

## 禁止

- 禁止删除/跳过测试让 CI 通过。
- 禁止无断言的测试。
- 禁止依赖执行顺序的测试。
- 禁止在测试中连接真实网络或写用户数据。