# 一日一页（myday）

单文件 PWA 日程应用：待办清单 + 时间轴 + 随手记。无构建步骤、无依赖、无框架。

## 部署（重要）

- **线上地址：https://lonelysauce94.github.io/myday/ ，由 GitHub Pages 从 `main` 分支直接发布。**
- 用户日常在 iPhone 上以 PWA 方式使用线上版本，不再本地打开 html 文件。
- 因此：**合并 / 推送到 `main` = 立刻上线**。改动先在功能分支验证好再进 main。
- **用户已明确授权（2026-07）：改动验证通过后直接合并进 `main` 上线，不用停下来等用户确认。** 只有大改版或有数据丢失风险的改动才先问。
- 用户电脑上的本地克隆只是 `main` 的一份镜像，用户自己 `git pull` 保持同步；不要假设本地有未推送的改动，也不需要为它做任何特殊处理。
- **每次改动页面后，必须把 `sw.js` 里的 `VERSION` 加 1**（如 `myday-v3` → `myday-v4`），否则已安装设备拿不到新页面。

## 文件结构

- `index.html` — 全部应用代码（HTML + CSS + JS 都在这一个文件里，约 3000 行）。
- `sw.js` — Service Worker：离线缓存，页面导航网络优先。
- `manifest.json`、`icons/` — PWA 安装所需。
- `部署到iPhone.md` — 用户的部署与云同步说明。
- 不要往仓库里放备份文件——git 历史就是备份，而且仓库里的一切都会被 Pages 公开发布。

## 代码约定

- 注释和 UI 文案全部用中文，保持现有口吻（口语化、解释"为什么"）。
- 数据存 `localStorage`（key `myday.v1`），跨设备同步只走 GitHub 私有 Gist 云同步（`cloudChip`）；改 `state` 结构要考虑旧数据迁移（见 `migrate` / `rolloverTasks`）。
- 「本地 data.json 数据文件」功能（File System Access API + `#fsChip` + 启动引导弹层）已于 2026-07 按用户要求整体移除，不要加回来；备份靠云同步菜单里的导出/导入。
- 时间以"当天分钟数"表示（`startMin`/`durMin`），吸附粒度 `SNAP = 15` 分钟。
- 手机版是同一页面的响应式变体：`@media (max-width: 760px)` + 底部 `#tabBar` 分页（清单 / 时间轴 / 随手记），三栏只显示其一。
  - 手机上任务**不能**拖到时间轴（不在同一屏），排时间靠 `◷` 按钮 → `#schedOverlay` 弹层；时间轴一侧靠 `#timeAddBar` 的「＋」→ `#pickOverlay` 选任务。
  - 桌面/手机差异用 `.only-mobile` / `.only-mobile-inline` / `.only-desktop` 控制。

## 验证方式

- 用预装 Chromium + 全局 Playwright（`require('/opt/node22/lib/node_modules/playwright/index.js')`）做端到端测试；手机流程用 `viewport: 390×844, isMobile: true, hasTouch: true`。
- 语法快查：把 `<script>` 内容抽出来 `new Function(src)` 即可。
