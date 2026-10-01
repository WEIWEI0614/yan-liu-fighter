# 闫祖 VS 刘祖 · 皇帝格斗

单文件自包含 HTML 小游戏（图片全部 base64 内嵌）。**双击 `index.html` 即可离线游玩，不需要任何服务器。**

---

## 一、在线玩（发给朋友用这个）

**https://weiwei0614.github.io/yan-liu-fighter/**

- 电脑、手机浏览器都能开；**在微信里点链接也能直接玩**（手机端会自动出触屏按钮）
- 首次加载约 2–15 秒（免费托管在境内的速度会波动），**页面上有加载进度条**，不是黑屏
- 已部署的是 `yan-liu-fight-protected.html`（混淆保护版）

---

## 二、操作

| | 闫祖（左 / 玩家1） | 刘祖（右 / 玩家2 或电脑） |
|---|---|---|
| 移动 | `A` / `D` | `←` / `→` |
| 跳 | `W` | `↑` |
| 防御 | 按「后」方向（或 `S` 蹲防） | 按「后」方向（或 `↓` 蹲防） |
| 轻掌 | `F` | `J` |
| 重掌 | `G` | `K` |
| 必杀① | `H` 万国来朝（全屏演出 + 高伤） | `L` 君临天下 |
| 必杀② | `T` **龟派气功**（锥形光束，射程全场，只有闫祖有） | — |

- 标题页按 **1** 单人对战电脑，按 **2** 双人同键盘
- 三局两胜，每局 60 秒
- 打击特效用颜色区分：**金色 = 你打中对方 · 红色 = 你被打 · 青色 = 被格挡**
- 任一方被 KO 后，会显示**该角色的倒地完结图**
- 手机上会出现触屏按键（多一个「波」键放龟派气功）

### 角色数值（v2 起）

| | 闫祖 | 刘祖 |
|---|---|---|
| 血量 | **1150** | 1000 |
| 攻击倍率 | **×1.15** | ×1.00 |
| 移速倍率 | **×1.10** | ×1.00 |
| 聚气效率 | **×1.20** | ×1.00 |
| 第二必杀 | **龟派气功**（265 基础伤害） | — |

> 闫祖的数值上调是应「VIP 客户」要求加的。想再调就改 `template.html` 里的 `CHARS` 表。
> 龟派气功是**直线判定**：只要对手在正面 940px 内且高度差不超过 330px 就必中；被防御伤害减半。

---

## 三、目录里的文件

| 文件 / 目录 | 说明 |
|---|---|
| `index.html` | 游戏本体（源码可读版本，本地玩用这个） |
| `yan-liu-fight-protected.html` | 混淆保护版（线上部署的就是它） |
| `ghsrc/` | 部署到 GitHub Pages 的仓库副本（含 `.git`） |
| `tools/` | 构建、压缩、混淆、自动化测试脚本 |
| `README.md` | 本文件 |

---

## 四、怎么更新线上版本

源码（真正的工程目录）在 `~/Desktop/harness/output/yan-liu-fight/`：
`template.html` 是模板，`assets/` 是素材，`tools/` 是流水线脚本。

```bash
cd ~/Desktop/harness/output/yan-liu-fight
python tools/build.py              # 素材注入 → index.html
node tools/verify_v2.mjs           # 功能断言（数值/龟派气功/KO 图/回归）
node tools/obfuscate.cjs           # 生成混淆保护版（体积只 +5%）
node tools/verify_protected.mjs    # ⚠️ 必跑：防混淆破坏逻辑
bash ~/Desktop/yan-liu-fight/tools/deploy.sh "提交说明"   # 发布
```

推送后约 1 分钟自动上线。

> **⚠️ 不要用 `git push`。** 本机 `github.com` 的 HTTPS 通路会被代理挡掉
> （报 `CONNECT tunnel failed, response 502` 或 `HTTP2 framing layer`），
> 而 `api.github.com` 是通的。所以部署走 `tools/deploy.sh` —— 它用 Git Data API
> 直接建 blob/tree/commit 并移动 ref，绕开 git 的传输通道。

> **⚠️ 验证线上时务必加缓存穿透参数**（如 `?t=$(date +%s)`）。GitHub Pages 的 CDN
> 会缓存旧版本，不加参数可能拿到几分钟前的页面，看起来像"没发布成功"。

---

## 五、关于代码保护

- 混淆做的三件事：标识符十六进制化、字符串搬进轮转数组、**代码被格式化/美化即自毁**
- 体积只比原版大 5%（726KB → 763KB）
- **前端代码无法真正保密** —— 浏览器必须拿到可执行代码，任何人按 F12 都能看到
- 混淆的作用是**把"复制走改个名就发"的成本抬高**，做不到绝对防护

---

## 六、分发方式实测结论（2026-10-01）

| 方式 | 微信里能玩吗 | 说明 |
|---|---|---|
| **GitHub Pages 链接** | ✅ | 当前方案，免费、长期有效 |
| 本地双击文件 | — | 自己玩，最稳，不需要网络 |
| 把 `.html` 文件直接发给别人 | ❌ **黑屏** | 微信的「文件预览」**不执行 JavaScript**；只有 http(s) 链接才会 |
| Cloudflare / Vercel / Netlify | ❌ | `workers.dev`、`pages.dev` 连不上；`netlify.app` 被解析到 `127.0.0.1` |
| jsDelivr | ❌ | 能通，但返回 `Content-Type: text/plain`，浏览器不会当网页渲染 |
| EdgeOne Pages 免费版 | ❌ | 只给几小时有效的临时预览链接，正式域名需**已备案**域名 |
| WorkBuddy 应用链接 | ⚠️ | 快且稳，但**一个工作区只能有一个应用**，发布即覆盖该工作区的现有应用 |

---

## 七、⚠️ 若要发布到 WorkBuddy（重要）

WorkBuddy 的发布是**按【工作区】绑定**的，**不是按目录**：

```
workspaceKey = sha256(工作区根目录绝对路径).hexdigest()[:16]
```

只要 `workspaceKey` 与某个已有应用相同，发布就会**覆盖它** —— 哪怕目录完全不同。
（2026-10-01 曾因误以为"按目录隔离"而覆盖过用户的「具身智能通勤包」，已恢复。）
官方文档确认：**发布工具永远只覆盖，没有参数能新建第二个应用**。

### 绝不能覆盖的 4 个已有应用

| workspaceKey | 应用 | 域名 |
|---|---|---|
| `e74fd595f63fe419` | 具身智能通勤包 | `embodied-ai-pack.app.workbuddy.host` |
| `fc212026443590eb` | 具身智能学习台 | `embodied-ai-study.app.workbuddy.host` |
| `292eff2887aa2db0` | 强化学习代码详解 | `rl-code-explained.app.workbuddy.host` |
| `395754f28307fab9` | 中学科目二高频题库 | `subject2-quiz-bank-64424.app.workbuddy.host` |

本目录预期 key：`e319daf614e9a8c7`，与上面 4 个均不冲突。

```bash
python3 -c "import hashlib;print(hashlib.sha256(b'/Users/davidwei/Desktop/yan-liu-fight').hexdigest()[:16])"
```

**若算出的 key 不是 `e319daf614e9a8c7`，先停下来问用户。**

---

## 八、关于微信小游戏

本网页版**无法转成微信小程序** —— 小程序是另一套代码（WXML/WXSS + `app.json`，不是 HTML/DOM）。
要做真正的小程序，需要在 WorkBuddy「代码开发 → 小程序」里另建项目，本游戏可作为玩法参考。
