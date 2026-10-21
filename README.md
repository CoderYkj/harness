scenes/
  ui/           可复用 UI 组件场景（按钮、面板、列表项）
  screens/      整屏/整页场景（主菜单、设置页、HUD）
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