# 80 - CI 与导出

## CI 目标

每次 PR / push 到 `main` 至少运行：

1. `godot --headless --path . --check-only`
2. `gdformat --check`
3. `gdlint`
4. GUT 测试

可选：Web 导出验证。

## GitHub Actions 示例（测试 + Lint）

```yaml
name: Godot CI
on:
  pull_request:
  push:
    branches: [main]

jobs:
  quality:
    runs-on: ubuntu-latest
    container:
      image: barichello/godot-ci:4.7.2  # 项目固定 Godot 4.7.2；若该 tag 未发布，用官方 4.7.2 发行包自建镜像
    steps:
      - uses: actions/checkout@v4
        with:
          lfs: true

      - name: Install gdtoolkit
        run: pip install "gdtoolkit==4.*"

      - name: Project check
        run: godot --headless --path . --check-only

      - name: Format check
        run: gdformat --check scripts/ tests/

      - name: Lint
        run: gdlint scripts/ tests/

      - name: Run GUT tests
        run: godot --headless -s addons/gut/gut_cmdln.gd -gdir=res://tests -gexit
```

`barichello/godot-ci` 是常用的 Godot CI 容器，适合无头运行测试和导出。GUT 的命令行模式支持 `-gdir` 和 `-gexit`，适合 CI 直接退出。

## 导出

- 导出预设放在 `export_presets.cfg`，由人工维护。
- Web 导出常用目标：`build/web/index.html`。
- 桌面导出按平台分目录：`build/windows/`、`build/linux/`、`build/macos/`。
- Agent **默认不执行导出**，除非任务明确要求且用户确认。
- 导出命令示例（需在 CI 或发布任务中运行）：

```bash
mkdir -p build/web
godot --headless --export-release "Web" build/web/index.html
```

## Agent 边界

- 可以读 CI 配置和导出预设，帮助定位问题。
- 不主动修改 `.github/workflows/`、`export_presets.cfg`、`project.godot` 的导出相关设置。
- 不在本地执行发布/上传 itch.io 等操作。