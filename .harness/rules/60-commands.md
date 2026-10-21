# 60 - 命令白名单

## 允许直接执行

```bash
godot --headless --path . --check-only
godot --editor --path .
godot --path .
godot --headless -s addons/gut/gut_cmdln.gd -gdir=res://tests -gexit
godot --headless -s addons/gut/gut_cmdln.gd -gtest=res://tests/unit/xxx.gd -gexit
gdlint scripts/ tests/
gdformat --check scripts/ tests/
gdformat scripts/ tests/
git status
git diff
git diff --stat
git log --oneline -n 20
git switch -c vibe/<slug>
```

## 需说明后执行

```bash
pip install "gdtoolkit==4.*"
godot --headless --export-release "Web" build/web/index.html
godot --headless --export-debug "Windows Desktop" build/windows/game.exe
git commit -m "..."
git push
```

## 禁止执行（除非用户明确授权）

```bash
git reset --hard
git clean -fdx
git push --force
rm -rf（项目外目录）
任何发布/部署命令
修改 project.godot 的 Autoload / 主场景
修改 export_presets.cfg
```

## verify 组合

```bash
godot --headless --path . --check-only \
  && gdformat --check scripts/ tests/ \
  && gdlint scripts/ tests/ \
  && godot --headless -s addons/gut/gut_cmdln.gd -gdir=res://tests -gexit
```

失败处理：读完整错误，判断是解析错误、场景加载错误、lint 问题还是测试失败；同一命令连续失败 2 次停止并汇报。