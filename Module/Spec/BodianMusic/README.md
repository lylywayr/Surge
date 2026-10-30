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
| `[Script] 波点音乐` | 远程引用 bdyy.js：会员调试、下载、付费专辑解锁、部分接口净化。`max-size=-1`（不限 body 大小），`timeout=60` |
| `[Script] 波点音乐-配置净化` | 本仓库 `bodian-clean.js`：删除 `/api/service/conf/all` 中的 `adStartAppDialog`、`adGuideTest`、`playTips`、`adExitChange`、`adGuidePopCount`、`adDownListOpen`，并关闭 P2P |
| `[URL Rewrite]` | 开屏广告相关接口直接返回空 JSON：`ad.tencentmusic.com/config/uni`、`bd-api.kuwo.cn/api/(play\|service\|advert)/advert/`、`h5s.kuwo.cn/m/bd_launch_popup/`、`tmeadcomm.y.qq.com` |
| `[MITM]` | `%APPEND%` 追加，不影响已有配置 |

## 排错

1. **装完必须彻底结束 App 进程**（上滑杀掉，不是切后台），广告配置有本地缓存。
2. 仍有开屏广告时，先在 Surge → 请求 里搜索 `kuwo` / `tencentmusic`，看开屏时命中的是哪个接口。
3. 模块内留了一条兜底规则（`api/service/finds/module?moduleId`），取消注释即可启用。
4. 若模块整体不生效，检查：MITM 证书信任、该模块已启用、`bd-api.kuwo.cn` 命中 [MITM]。

## 说明

- 原脚本 MITM 列表中的裸 IP（含 `:443` / `:80` 端口）条目已剔除：Surge 的 MITM hostname 不接受 `host:port`，且按 IP 做 MITM 会因证书域名不匹配置信失败；业务域名已覆盖脚本匹配的全部接口。
- `max-size` 官方语义：默认 1 MB，`-1` 表示不限制；`0` 不是“不限制”，会导致脚本被跳过。
