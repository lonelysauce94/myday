# Design Handoff:「我的一天」UI 优化

## 项目背景

这是一个单文件的个人日程 app(index.html),桌面端使用,布局为一屏三栏:

- **左栏(330px)**:今日焦点(最多 3 件)+ 待办清单 + 已完成折叠区
- **中栏(自适应)**:当日时间轴,任务块按时间定位,有红色"现在"线
- **右栏(250px)**:随手记 textarea

整个界面高度固定一屏(body 100vh、overflow hidden),只有待办清单和已完成区内部可滚动。所有界面内容(任务条、时间块、菜单)都由 `<script>` 里的 JS 动态生成,数据存 localStorage。

## 任务范围

**要做**:只优化视觉设计——配色、字体排印、间距节奏、圆角阴影、交互态(hover/选中/拖拽中)的视觉反馈、整体质感。

**不要做**:
- 不改 `<script>` 块里的任何 JS 逻辑,理想情况下 script 一个字都不动
- 不增删功能、不改交互流程、不改文案
- 不引入外部依赖(CDN、字体外链、框架都不要),保持单文件、双击就能打开
- 不做移动端适配(交互基于鼠标 mousedown 拖拽,桌面专用)

## 设计方向(请按喜好修改这段)

保持温和护眼的纸感基调,但希望整体更精致:层次感更清晰、焦点区更突出、时间块的完成/未完成状态对比更明显。中文界面,字体栈必须包含中文字体(现为 "Segoe UI", "Microsoft YaHei")。

## 硬性技术约束(违反任何一条 app 会坏)

### 1. localStorage 不可动
key 为 `myday.v1`,数据结构不可更改,否则用户现有数据丢失。

### 2. 以下 id 必须原样保留(JS 用 getElementById/querySelector 引用)

`prevDay` `todayBtn` `nextDay` `dateLabel` `dayStart` `dayEnd` `addInput` `addBtn` `todoList` `focusList` `focusSection` `inboxSection` `doneSection` `doneList` `doneCount` `doneHeader` `clearDone` `timeline` `hourRows` `blocksArea` `dropGhost` `nowLine` `emptyHint` `notesArea` `blockMenu` `left` `right` `notesPanel`

另有两个元素由 JS 动态创建并依赖 CSS 中的 id 选择器:`#dropIndicator`(列表排序插入线)、`#dragPill`(拖拽跟随标签)——这两个 id 的样式规则必须保留。

### 3. 以下 class 名由 JS 动态创建或切换,必须保留且有对应样式

- 任务条:`.todo` `.todo.done` `.title` `.tag` `.sched-tag`(排程标签)
- 焦点区:`.focus-item` `.focus-item.done` `.focus-num` `.focus-empty`
- 勾选框:`.check` `.check.on`
- 图标按钮:`.icon-btn` `.icon-btn.star-on`
- 时间块:`.block` `.block.done` `.block.focus-block` `.block.selected` `.b-title` `.b-time` `.resize-handle`
- 时间轴:`.hour-row` `.hlabel` `.half-line`
- 拖拽态:`.dragging`(半透明)、`body.is-dragging`(grabbing 光标)
- 其他:`.rename-input`(双击改名的行内输入框)、`#doneList.open`、`#blockMenu.open`、`#blockMenu .m-danger` `.m-dur`

### 4. 定位机制不可破坏

- `#timeline` 必须保持 `position: relative`、`#blocksArea` 保持 `position: absolute` 铺满——JS 用百分比设置时间块的 top/height/left/width
- JS 中写死了 `ghost.style.left = '52px'`,与 CSS 中 `#blocksArea { left: 52px }` 和 `.hlabel { width: 48px }` 对应。**请不要改动时间标签列的宽度**;如确需改,必须让这三处数值保持同步关系
- `#blockMenu` `#dropGhost` `#nowLine` `#dragPill` 的 display/定位由 JS 内联控制,CSS 里不要用 `!important` 或改变它们的显示机制

### 5. 布局约束

- 整体必须保持一屏(100vh)不出现页面级滚动条,只有 `#todoList` 和 `#doneList` 内部滚动
- 现有 CSS 变量体系(`--bg` `--accent` `--focus` 等)建议沿用并在此基础上调整取值,这是最安全的改法

## 交付物

修改后的完整单文件 index.html。

## 验收清单(改完请自查)

1. 输入框回车/点 + 能添加任务
2. 勾选完成 → 任务进已完成区,点标题可展开/折叠,"清空"可用
3. 点 ★ 设为焦点(最多 3 件),焦点区显示编号
4. 双击任务名可行内改名
5. 从清单拖任务到时间轴 → 生成时间块;拖动块可改时间;拖块下边缘可调时长
6. 清单内上下拖动可排序(有插入指示线)
7. 双击时间轴空白弹出快速创建
8. 点时间块弹出操作菜单(完成/±30m/改名/移回清单/删除)
9. 日期前后切换、"今天"按钮、显示时段下拉均正常
10. 随手记输入后刷新页面内容还在;所有任务数据刷新后不丢
