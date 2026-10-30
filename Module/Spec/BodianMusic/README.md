# 波点音乐 高级功能（Surge 模块）

由 bdyy.js 头部自带的 `[rewrite_local]` / `[mitm]` 规则转换，并补充开屏广告拦截的 Surge 原生模块。

- 脚本作者：WeiGiegie
- 原脚本：https://raw.githubusercontent.com/WeiGiegie/666/main/bdyy.js
- 功能：会员状态调试、去广告（含开屏广告）、歌曲下载（含付费专辑导出）、付费专辑解锁

## 安装

Surge → 首页 → 模块 → 安装新模块 → 填入：

```
https://raw.githubusercontent.com/lylywayr/Surge/Master/Module/Spec/BodianMusic/BodianMusic.sgmodule
```

## 结构

| 部分 | 作用 |
| --- | --- |
| `[Script] 波点音乐` | 远程引用 bdyy.js：会员调试、下载、付费专辑解锁、酷我广告接口净化。`max-size=-1`（不限 body），`timeout=60` |
| `[Script] 波点音乐-配置净化` | `bodian-clean.js`：删 `/api/service/conf/all` 的 `adStartAppDialog`、`adGuideTest`、`playTips`、`adExitChange`、`adGuidePopCount`、`adDownListOpen`，关闭 P2P |
| `[URL Rewrite]` | 腾讯音乐广告 SDK、广点通 SDK、广告素材 CDN、酷我 advert 接口、开屏弹窗 H5 全部返回空 |
| `[MITM]` | `%APPEND%` 追加，不影响已有配置 |

## 开屏广告定位结论（2026-10-30 抓包）

一次完整启动抓包（160 条请求）显示：

- 酷我自己的广告接口（`/api/popup/start/info`、`/api/service/advert/config`、`/api/advert/free/config`、`/abtest/ui/info`）**已经全部返回空**；
- `/api/service/conf/all` 中 `adStartAppDialog` 已不存在、`flowControlConfig.p2pEnable=false` → 说明配置净化脚本确实生效；
- 仍在工作的广告链路只有一个：**腾讯音乐广告 SDK**
  - `mc.tencentmusic.com/config/uni`
  - `mc.tencentmusic.com/sdk/config`
  - `mc.tencentmusic.com/sdk/ad/strategies`
  - 素材：`ossgw.alicdn.com/creatives-assets/...jpg`（1080×1920，标准开屏尺寸）
  - 广点通：`xs.gdt.qq.com/style_factory/*`

`mc.tencentmusic.com` 的响应体是 SDK 自加密的密文（base64），无法按字段改写，因此只能整域名拦截；`ad.tencentmusic.com` 在抓包里根本没有出现，早期那条规则属于无效规则。

## 排错

1. **改动后必须彻底结束 App 进程**（上滑杀掉），并最好「设置 → 清除缓存」或重装——开屏素材是提前缓存的，不失效就会继续播。
2. 仍有开屏广告时重新抓包：请求页 → 清除 → **先杀 App** → 停在 Surge → 打开 App → 再导出 HAR。
3. 模块内留了兜底规则（`api/service/finds/module?moduleId`），取消注释即可启用。

## 说明

- 原脚本 MITM 列表中的裸 IP（含 `:443` / `:80` 端口）条目已剔除：Surge 的 MITM hostname 不接受 `host:port`，且按 IP 做 MITM 会因证书域名不匹配置信失败；业务域名已覆盖脚本匹配的全部接口。
- `max-size` 官方语义：默认 1 MB，`-1` 表示不限制；`0` 不是“不限制”，会导致脚本被跳过。
