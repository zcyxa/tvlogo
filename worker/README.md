# Worker.js

只保留 `epg.js`：支持 DIYP 类播放器的 EPG 服务。

## 部署

1. 登录 https://www.cloudflare.com → 左侧 `Workers 和 Pages`
2. 右上角 `创建应用程序` → `创建 Worker` → `部署`
3. 点 `编辑代码`，把 `epg.js` 全部内容粘贴进去覆盖
4. 右上角 `部署` → `保存并部署`
5. 返回后进 `设置` → `触发器` → `添加自定义域名`，绑定自己的域名

## 用法

```
https://<worker 域名>/?ch=CCTV1&date=20261105   # DIYP 格式 JSON
https://<worker 域名>/                          # 302 到 e.xml
```