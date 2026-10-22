# harness — Godot 4.7.2 前端项目脚手架

> 面向 **Godot 4.7.2 + 纯 GDScript** 的 UI / 场景驱动前端项目骨架，配套一套人类与 AI Agent 共用的工程规范。
> 设计取向：场景结构浅而清晰、主题资源集中管理、信号解耦、数据驱动 UI。

**引擎版本：Godot 4.7.2（stable，固定）** —— 本项目只使用这一个版本；`project.godot`、本机工具链、CI 镜像三者必须一致，不使用「4.x 随便」的泛化说法。

当前仓库还是一个**脚手架（scaffold）**：规则、协作入口与任务配置已就位，但业务目录尚未创建，`project.godot` 仍是空文件。它不是可以直接运行的成品项目。

---

## 一、当前状态

| 组成部分 | 现状 |
|---|---|
| 引擎版本 | **Godot 4.7.2 stable**，全项目唯一允许版本（见第十节版本策略） |
| `project.godot` | **空文件**，首次用编辑器打开时会补全项目配置，需确认写入 4.7.2 对应的 `config_version` / `features` |
| `scenes/` `scripts/` `resources/` `assets/` `tests/` | 尚未创建（结构约定见下方） |
| `AGENTS.md` | 已就绪：Agent 总入口 |
| `.harness/rules/` | 已就绪：10 份规则（00 ~ 80） |
| `.vscode/tasks.json` | 已就绪：项目检查 / 格式检查 / Lint / GUT / Vibe Coding 共 5 个任务 |
| GUT 测试框架 `addons/gut/` | 未引入 |
| CI `.github/workflows/` | 未创建（示例见 `.harness/rules/80-ci-export.md`） |
| Git | `harness/` 已初始化 `.git`，remote 指向 `CoderYkj/harness`；`repo/` 是内容相同的镜像目录，无独立 `.git` |

> 已知差异：仓库实际默认分支为 `master`，而 `AGENTS.md` 约定主分支为 `main`，两者需要统一（推荐以 `main` 为准）。

---

## 二、环境要求

| 依赖 | 版本 / 说明 |
|---|---|
| Godot | **4.7.2 stable（固定）**，下载见 [Godot 4.7.2 归档](https://godotengine.org/download/archive/4.7.2-stable/)，不要用 4.6/4.8 等其它版本替代 |
| GDScript | 纯 GDScript，不引入 C# |
| Python | 3.x，用于安装 `gdtoolkit` |
| gdtoolkit | `pip install "gdtoolkit==4.*"`，提供 `gdlint` / `gdformat` |
| GUT | GUT 9.x（Godot 4 分支），放在 `addons/gut/` |
| Git | 仓库与分支操作 |

当前开发机检测结果：`godot`、`gdlint`、`gdformat` **均未安装**，`python` / `pip` 可用；安装 Godot 时请精确到 4.7.2 版本。首次上手请先完成下节第 1、4、5 步。

---

## 三、从脚手架到可运行

```bash
# 1. 安装 Godot 4.7.2 stable，确认版本号完全一致
godot --version
# 期望输出：4.7.2.stable.<hash> 之类，前缀必须是 4.7.2.stable

# 2. 首次打开编辑器，生成 / 补全 project.godot，并设置主场景
godot --editor --path .

# 3. 创建约定目录（见第四节）
#    scenes/{ui,screens,components}  scripts/{ui,screens,components}
#    resources/{data,themes,config}  assets/{textures,fonts,audio}  tests/{unit,integration}

# 4. 引入 GUT（Godot 4 分支）到 addons/gut/
#    方式任选：复制发布包 / git submodule / Godot AssetLib 安装

# 5. 安装代码质量工具
pip install "gdtoolkit==4.*"

# 6. 全量校验（无头检查 + 格式 + Lint + 测试）
godot --headless --path . --check-only \
  && gdformat --check scripts/ tests/ \
  && gdlint scripts/ tests/ \
  && godot --headless -s addons/gut/gut_cmdln.gd -gdir=res://tests -gexit
```

---

## 四、目录结构

```text
scenes/
  ui/           可复用 UI 组件场景（按钮、面板、列表项）
  screens/      整屏 / 整页场景（主菜单、设置页、HUD）
  components/   非 UI 通用组件场景（弹窗、提示条）
scripts/
  ui/           与 scenes/ui 对应的脚本
  screens/      与 scenes/screens 对应的脚本
  components/
resources/
  data/         .tres 数据资源（配置、主题数据、本地化）
  themes/       Theme / StyleBox 资源
  config/
assets/
  textures/ fonts/ audio/
addons/
  gut/          测试框架
tests/
  unit/         纯逻辑测试
  integration/  场景加载、信号连接、UI 交互测试
.harness/
  rules/        Agent 规则 00 ~ 80
AGENTS.md       Agent 总入口
```

约定要点：

- 场景与脚本**同构命名**：`settings_panel.tscn` ↔ `settings_panel.gd`。
- 可复用 UI 组件必须是独立场景；整屏场景放 `scenes/screens/`。
- 脚本访问节点优先用 **Scene Unique Name**（`%TitleLabel`），不用绝对路径。
- 主题、字体、StyleBox 统一放 `resources/themes/`，不在场景里散落硬编码颜色与字号。
- 持久化数据只写 `user://`，不把 `res://` 路径硬编码进保存逻辑。

---

## 五、常用命令

| 目的 | 命令 |
|---|---|
| 版本确认 | `godot --version`（必须是 `4.7.2.stable`） |
| 编辑器打开 | `godot --editor --path .` |
| 项目可加载检查 | `godot --headless --path . --check-only` |
| 运行主场景 | `godot --path .` |
| 运行全部测试 | `godot --headless -s addons/gut/gut_cmdln.gd -gdir=res://tests -gexit` |
| 运行单个测试 | `godot --headless -s addons/gut/gut_cmdln.gd -gtest=res://tests/unit/test_xxx.gd -gexit` |
| Lint | `gdlint scripts/ tests/` |
| 格式检查 | `gdformat --check scripts/ tests/` |
| 格式化 | `gdformat scripts/ tests/` |
| 全量校验 | 见下方 `verify` |

`verify` 组合（在项目根执行，提交前必过）：

```bash
godot --headless --path . --check-only \
  && gdformat --check scripts/ tests/ \
  && gdlint scripts/ tests/ \
  && godot --headless -s addons/gut/gut_cmdln.gd -gdir=res://tests -gexit
```

VS Code 里可用 `.vscode/tasks.json` 中的同名任务直接运行。

---

## 六、测试（GUT）

- 框架：**GUT**（Godot 4 分支，纯 GDScript 项目首选，当前不混用 gdUnit4）。
- 要求：新 UI 组件必须有测试；Bug 修复先写失败测试；测试无网络、无真实时间依赖。
- 测试节点用 `add_child_autofree` 保证释放；禁止删除 / 跳过测试让 CI 通过。

```gdscript
extends GutTest

func test_primary_button_emits_pressed_signal() -> void:
    var button := preload("res://scenes/ui/primary_button.tscn").instantiate()
    add_child_autofree(button)
    button.pressed.emit()
    assert_true(button.is_pressed, "button should be pressed")
```

---

## 七、CI 与导出

- CI 目标（PR / push 到主分支）：`--check-only` → `gdformat --check` → `gdlint` → GUT，全部在 **Godot 4.7.2** 环境下运行。
- 参考实现：`.harness/rules/80-ci-export.md` 中的 GitHub Actions 示例。容器镜像必须提供 4.7.2，例如 `barichello/godot-ci:4.7.2` —— 使用前先确认该 tag 已发布，否则改用自建镜像或官方发行包；**该 workflow 尚未落地**，需自行创建 `.github/workflows/`。
- 导出由人工维护 `export_presets.cfg`；产物统一进 `build/web/`、`build/windows/` 等目录。
- 默认不自动导出、不部署到 itch.io 等平台。

---

## 八、面向 AI Agent 与协作者

**阅读顺序**：`AGENTS.md` → `.harness/rules/00-global.md` → `10-workflow` → `20-gdscript-style` → `30-scene-resource` → `40-testing` → `50-git` → `60-commands` → `70-security-forbidden` → `80-ci-export` → 相关 `.tscn` / `.gd` / `.tres` / 测试。

**工作方式**：理解任务 → 读上下文（`project.godot`、场景、脚本、测试）→ 给 ≤10 行计划 → 小步实施并逐步 `--check-only` → 收尾跑 `verify` → 输出变更摘要，**等待人工审 diff**。

**边界**（需先说明理由并请求确认）：

- 修改 Autoload、主场景、项目设置、渲染设置；
- 修改 `export_presets.cfg`、`.github/workflows/`、CI 配置；
- 新增插件或依赖、大范围重排场景节点。

**Git 约定**：

- 分支：`vibe/<slug>`（Agent 专用）、`feat/<slug>`、`fix/<slug>`，从主分支拉出、PR 回主分支。
- 提交信息遵循 Conventional Commits：`feat(ui): add settings panel toggle`、`fix(scene): resolve null label on ready`。
- 一个提交只做一件事；提交前必须通过 `verify`；不提交 `.godot/`、`.import/`、导出产物与密钥。
- Agent **默认不 commit、不 push、不打 tag、不导出**，除非用户明确要求。

**安全红线**：不读取 / 输出 / 提交密钥与用户数据；不执行 `git reset --hard`、`git clean -fdx`、`git push --force`；不删除或重命名 `addons/` 中的测试框架文件。

---

## 九、下一步

- [ ] 安装 **Godot 4.7.2 stable**，用编辑器初始化 `project.godot` 并设置主场景
- [ ] 创建第四节中的目录骨架与首个 UI 组件场景
- [ ] 引入 GUT（Godot 4 分支）到 `addons/gut/`，补 `tests/unit/` 首个用例
- [ ] 创建 `.github/workflows/`，在 4.7.2 镜像上跑通 `verify` 四步
- [ ] 统一默认分支（`master` → `main`），与 `AGENTS.md` 对齐
- [x] 同步 `AGENTS.md`、`80-ci-export.md` 中的引擎版本为 4.7.2（已完成；`60-commands.md` 不含版本文本）

---

## 十、版本策略

1. **唯一版本**：Godot **4.7.2 stable**。文档、CI、本机、`project.godot` 四处保持一致。
2. **禁止随手升级**：升级引擎属于项目级变更，需用户明确同意，且同一提交内同步更新 `project.godot`、`.github/workflows/`、`README.md`、`AGENTS.md`、`.harness/rules/80-ci-export.md`。
3. **升级前先跑基线**：旧版本下 `verify` 必须全绿，升级后同命令复跑，输出差异摘要。
4. **版本号写法**：一律写 `4.7.2`（或 `4.7.2 stable`），不写 `4.x`、`4.7+` 这类模糊表述。
5. **文档一致性**：任何版本变更后，搜索仓库中的 `4.` 版本串统一替换，避免文档与 CI 漂移。
