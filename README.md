# 电视直播台标与 EPG

一个 **纯静态** 的台标库 + EPG 节目单仓库：只放图片和节目单文件，不需要服务器、不需要数据库，直接走 GitHub Pages / jsDelivr / 任意 GitHub 加速节点给播放器调用。

- **台标**：`tv/` 946 个电视频道台标（PNG），`radio/` 470 个电台图标
- **EPG**：XMLTV 格式节目单，每 2 小时自动更新，同时提供 `.xml` 与 `.xml.gz`
- **接口**：`worker/epg.js`（Cloudflare Worker，DIYP 格式 JSON 接口）

> 台标 / 节目单版权归各电视台与原始整理者所有，本仓库仅做公开资源汇总，如有侵权请联系删除。

---

## 目录结构

| 路径 | 说明 |
|---|---|
| `tv/` | 电视频道台标，文件名即频道名（如 `CCTV1.png`、`湖南卫视.png`） |
| `radio/` | 电台图标（PNG） |
| `worker/epg.js` | EPG 接口（Cloudflare Worker，返回 DIYP 格式 JSON） |
| `worker/README.md` | Worker 部署步骤 |
| `.github/workflows/epg.yml` | EPG 自动更新：拉取源站 → 生成 `.xml` / `.xml.gz` → 强推 `data` 分支 |
| `index.html` | 本仓库的展示首页（GitHub Pages） |

**EPG 数据放在 `data` 分支**（不在 `main`）：每次更新强制覆盖，历史永远只有一份，仓库不会越滚越大。

```
main  →  代码 / 台标 / 说明
data  →  e.xml（9.4 MB）、e.xml.gz（825 KB）
```

---

## 一、台标怎么用

台标地址是一个**模板**，把 `{name}` 换成频道名即可：

```
https://zcyxa.github.io/tvlogo/tv/{name|uppercase}.png
```

`{name|uppercase}` 表示取频道名（大写形式）；例如：

| 频道 | 地址 |
|---|---|
| CCTV1 | `https://zcyxa.github.io/tvlogo/tv/CCTV1.png` |
| 湖南卫视 | `https://zcyxa.github.io/tvlogo/tv/湖南卫视.png` |

### 国内访问慢？换加速地址

| 方式 | 模板地址 | 说明 |
|---|---|---|
| jsDelivr | `https://gcore.jsdelivr.net/gh/zcyxa/tvlogo@main/tv/{name\|uppercase}.png` | CDN，缓存后约 200 ms |
| gh-proxy | `https://gh-proxy.com/https://raw.githubusercontent.com/zcyxa/tvlogo/main/tv/{name\|uppercase}.png` | 国内中转，实时 |

> `fastly.jsdelivr.net`、`cdn.jsdelivr.net` 也可以，把 `gcore` 换掉即可。

---

## 二、EPG 怎么用

**推荐（压缩版，约 825 KB）**：

```
https://gcore.jsdelivr.net/gh/zcyxa/tvlogo@data/e.xml.gz
```

原始 XMLTV（约 9.4 MB）：

```
https://gcore.jsdelivr.net/gh/zcyxa/tvlogo@data/e.xml
```

国内中转版本：

```
https://gh-proxy.com/https://raw.githubusercontent.com/zcyxa/tvlogo/data/e.xml.gz
```

注意：

- 播放器要支持 `.gz`（本项目配套的 Android TV 客户端可以，URL **必须以 `.gz` 结尾**，后面不要加 `?v=xxx`）
- 数据每 2 小时更新一次，更新后自动清理 jsDelivr 缓存，不会滞后
- GitHub Pages 只发布 `main` 分支，所以 `zcyxa.github.io/.../e.xml` 是 **404**，请使用上面的 `@data` 地址

### 自建 DIYP 接口（可选）

把 `worker/epg.js` 部署到 Cloudflare Workers，即可得到：

```
https://<你的 worker>/?ch=CCTV1&date=20261105   → DIYP 格式 JSON
https://<你的 worker>/                          → 302 跳转到 e.xml
```

部署步骤见 [`worker/README.md`](worker/README.md)。

---

## 三、自动更新

`.github/workflows/epg.yml`：

1. 每 2 小时（UTC 偶数点 `:15`）以及每次推送 `main` 时触发
2. 从 `suzukua/epg` 的 `hidden` 分支拉取 `t.xml`
3. 生成 `e.xml` 与 `e.xml.gz`
4. **强制推送**到 `data` 分支（单条提交，不累积历史）
5. 调用 `purge.jsdelivr.net` 清掉 jsDelivr 缓存

也可以在 **Actions → EPG Update → Run workflow** 手动触发一次。

---

## 四、使用约定

- 台标文件名与频道名一一对应；需要新增台标时直接把图片放进 `tv/`，文件名用频道名
- 建议用 `.png`，尺寸 512×512 以内，单张控制在 100 KB 以下
- 请不要把本仓库当图床跑超大流量（GitHub Pages 每月约 100 GB 软限额）

## 免责声明

本仓库仅收录公开可访问的电视 / 广播台标与节目单信息，所有商标与台标版权归各自权利人所有。仓库不提供、不托管任何直播流地址。如权利人认为内容侵权，请提交 Issue 或联系仓库所有者，核实后会立即删除。
