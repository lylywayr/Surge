# 今日水印相机 去广告（Surge 模块）

将 [ZenmoFeiShi/Qx](https://github.com/ZenmoFeiShi/Qx) 的 Quantumult X snippet `xhey-ad-clean` 转换为 Surge 模块。

## 功能

今日水印相机（XCamera 3.0.480）开屏、插屏、信息流与 RTB 广告净化：

- **9 条 URL 重写拒绝**：拦截今日水印相机自身广告接口（`ad.xhey.top`、`39.108.85.131`）以及 RTB 广告 SDK 上报（`sdk.1rtb.net`、`statisticslinks.com` 系列、腾讯 `sdkquic.e.qq.com`、百度 `als.baidu.com`）。
- **1 条响应脚本**：改写 `jt-sdk-ios.oss-cn-beijing.aliyuncs.com/iOSConfig.txt` 的广告配置，关闭 App Store 优化、广告弹窗、开屏/插屏/激励视频展示。

## 安装

```
https://raw.githubusercontent.com/lylywayr/Surge/Master/Module/Spec/TodayWatermarkCamera/TodayWatermarkCamera.sgmodule
```

jsDelivr 镜像：

```
https://cdn.jsdelivr.net/gh/lylywayr/Surge@Master/Module/Spec/TodayWatermarkCamera/TodayWatermarkCamera.sgmodule
```

Surge → 首页 → 模块 → 从 URL 安装，粘贴上面任意一条即可。安装后确认 MITM 已开启（模块会自动追加域名 `jt-sdk-ios.oss-cn-beijing.aliyuncs.com`）。

## 说明

- 原 QX 的 `url reject` 对应 Surge 的 `[URL Rewrite] ... - reject`，不依赖 MITM。只有响应脚本需要 MITM，因此 `hostname` 只保留了脚本域名，不会扩大解密范围。
- `ad-clean.js` 为原脚本副本（原样保留，兼容 Surge 的 `$response` / `$done`），托管在本仓库以避免上游仓库变动导致模块失效；上游更新时可用原链接替换 `script-path`。
- 原始来源：<https://raw.githubusercontent.com/ZenmoFeiShi/Qx/main/xhey-ad-clean.snippet>
- 脚本作者：TG@ZenMoFiShi
