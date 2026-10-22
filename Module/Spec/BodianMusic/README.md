# 波点音乐 高级功能（Surge 模块）

由 bdyy.js 头部自带的 `[rewrite_local]` / `[mitm]` 规则转换而来的 Surge 原生模块。

- 脚本作者：WeiGiegie
- 原脚本：https://raw.githubusercontent.com/WeiGiegie/666/main/bdyy.js
- 功能：会员状态调试、去广告、歌曲下载（含付费专辑导出）、付费专辑解锁

## 安装

Surge → 首页 → 模块 → 安装新模块 → 填入：

```
https://raw.githubusercontent.com/lylywayr/Surge/Master/Module/Spec/BodianMusic/BodianMusic.sgmodule
```

## 说明

- `[Script]` 直接远程引用作者脚本，作者更新脚本后模块无需更新。
- `[URL Rewrite]` 中的 `reject-200` 用于拦截 `tmeadcomm.y.qq.com` 的广告请求。
- 原脚本 MITM 列表中的裸 IP（含 `:443` / `:80` 端口）条目已剔除：Surge 的 MITM hostname 不接受 `host:port`，且按 IP 做 MITM 会因证书域名不匹配置信失败；业务域名（`*.kuwo.cn` / `*.kuwo.com` / `*.l.qq.com` 等）已覆盖脚本匹配的全部接口。
- 本模块仅在 Surge 下验证通过，Egern 亦可直接使用同一文件。
