# 台标与 EPG

电视频道台标 / 电台图标，以及 EPG 节目单与 EPG 接口。

| 路径 | 说明 |
|---|---|
| `tv/` | 电视频道台标（PNG，文件名即频道名） |
| `radio/` | 电台图标（PNG） |
| `e.xml` | EPG 节目单（XMLTV），由 GitHub Actions 每 2 小时自动更新 |
| `worker/epg.js` | EPG 接口（Cloudflare Worker，返回 DIYP 格式 JSON） |
| `.github/workflows/epg.yml` | 自动拉取并提交 `e.xml` |

## 台标

```
https://zcyxa.github.io/tvlogo/tv/<频道名>.png
https://zcyxa.github.io/tvlogo/radio/<电台名>.png
```

## EPG

- 原始 XMLTV：`https://zcyxa.github.io/tvlogo/e.xml`
- DIYP 接口（把 `worker/epg.js` 部署到 Cloudflare Workers 后）：

```
https://<worker 域名>/?ch=CCTV1&date=20261105
```