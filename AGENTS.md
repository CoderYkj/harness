# AGENTS.md

本文件是 AI Agent（DeepSeek Harness / CLI Agent）在本 Godot 4 前端项目工作的**总入口**。
任务开始前，必须先读本文件，再按顺序读取 `.harness/rules/` 下的规则。

---

## 1. 项目概述

- 引擎：Godot 4.x（固定版本，见 `project.godot` 或 CI 配置）
- 语言：GDScript（纯 GDScript 项目，不含 C#）
- 定位：前端 / UI / 场景驱动，重视场景结构、主题资源、信号解耦
- 测试：GUT（Godot Unit Testing），纯 GDScript 项目首选
- Lint / 格式化：`gdtoolkit`（`gdlint` + `gdformat`），可选 `gdcorrect`
- CI：GitHub Actions，无头模式运行测试、lint、导出 Web/桌面构建
- 主分支：`main`
- Agent 专用分支前缀：`vibe/`

> 如果实际 Godot 版本、测试框架或导出目标不同，同步修改本节与 `60-commands.md`、`80-ci-export.md`。

---

## 2. 快速命令

| 目的 | 命令 |
|---|---|
| 编辑器打开 | `godot --editor --path .` |
| 无头检查项目可加载 | `godot --headless --path . --check-only` |
| 运行主场景 | `godot --path .` |
| 运行测试（GUT） | `godot --headless -s addons/gut/gut_cmdln.gd -gdir=res://tests -gexit` |
| 单文件测试 | `godot --headless -s addons/gut/gut_cmdln.gd -gtest=res://tests/unit/test_xxx.gd -gexit` |
| Lint | `gdlint scripts/ tests/` |
| 格式检查 | `gdformat --check scripts/ tests/` |
| 格式化 | `gdformat scripts/ tests/` |
| 全量校验 | 见 `60-commands.md` 中的 `verify` 组合命令 |

`verify` 推荐组合（在项目根执行）：

```bash
godot --headless --path . --check-only \
  && gdformat --check scripts/ tests/ \
  && gdlint scripts/ tests/ \
  && godot --headless -s addons/gut/gut_cmdln.gd -gdir=res://tests -gexit