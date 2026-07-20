# 把「一日一页」装到 iPhone

三步：**推到 GitHub → 开 Pages → iPhone 添加到主屏幕**。之后再连一次云同步就好了。

---

## 第一步：推到 GitHub

本地仓库已经建好并提交过了，只差一个远程地址。

1. 打开 https://github.com/new
2. 仓库名随便取，比如 `myday`
3. **选 Public**（免费账号的 GitHub Pages 只支持公开仓库）
4. 不要勾 "Add a README file"，直接 Create
5. 回到电脑，在项目文件夹里执行（把 `你的用户名` 换掉）：

```bash
git remote add origin https://github.com/你的用户名/myday.git
git push -u origin main
```

推送时会弹出 GitHub 登录，照着走完即可。

> 仓库是公开的，但里面**只有代码，没有你的待办数据**。`.gitignore` 已经把 `.claude/`
> 和所有数据 json 排除掉了。数据只存在你自己的浏览器和私有 Gist 里。

## 第二步：开启 GitHub Pages

1. 仓库页面 → **Settings** → 左侧 **Pages**
2. Source 选 **Deploy from a branch**
3. Branch 选 **main**，文件夹选 **/ (root)**，Save
4. 等一两分钟，页面顶部会出现网址：
   `https://你的用户名.github.io/myday/`

## 第三步：iPhone 添加到主屏幕

1. 用 **Safari**（必须是 Safari，Chrome 不行）打开上面那个网址
2. 点底部中间的**分享**按钮
3. 往下滑，选 **添加到主屏幕**
4. 确认，主屏幕上就出现图标了

从图标打开是全屏的，没有地址栏，和普通 app 一样。

---

## 最后：连接云同步（电脑和 iPhone 各做一次）

### 先生成一个 token

1. 打开 https://github.com/settings/tokens?type=beta
2. **Generate new token**
3. Expiration 建议选 **No expiration**（不然到期要重连）
4. 权限：找到 **Account permissions** → **Gists** → 设为 **Read and write**
5. 生成后把那串 token 复制下来，**只显示这一次**，建议存进密码管理器

### 两台设备都连上

1. 点右上角 **云同步**
2. Token 粘贴进去，**Gist ID 留空**
3. 点连接

第一台会自动新建一个私有 Gist；第二台贴同一个 token 时会自动找到它，然后把两边数据合并——两边的任务都不会丢。

连上以后 chip 会显示「● 已同步」。

---

## 几个要知道的点

**同步什么时候发生** —— 你每次改动，1.2 秒后自动上传；每次打开 App 或切回 App 时自动下载。iOS 的 PWA 不能后台同步，所以是"打开就同步"，不是"实时推送"。

**断网了怎么办** —— 照常用。改动存在手机本地，chip 会显示「⚠ 待补传」，一联网自动补上去。

**手机上怎么排时间** —— 手机是分三个标签页的，没法把任务从「清单」拖到「时间轴」。改成点任务右边的 **◷** 按钮选时间。在「时间轴」标签里，长按空白往下拖画时间段、拖动时间块调整，这些照常能用。

**「显示时段」在手机上看不到** —— 屏幕放不下，去电脑上改，设置会同步过来。

**改了代码怎么更新** —— 改完 `git push`，等一分钟，手机上把 App 划掉重开。如果没更新，把 `sw.js` 里的 `VERSION` 加 1 再推一次。

**数据会不会丢** —— 主屏幕 App 不受 Safari「7 天不用清存储」的限制。但**删掉主屏幕图标 = 本机数据一起没**。有云同步兜底，重新装上贴回 token 就能拉回来。真正要保管好的是 token 本身。建议偶尔在电脑上「导出备份 (.json)」存一份。
