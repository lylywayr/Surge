# WPS 签到（Surge 模块）

WPS Office 每日签到 + 福利中心任务 + 小程序打卡，送 WPS 积分与超级会员时长。

脚本来自 [MaYIHEI/paperclip](https://github.com/MaYIHEI/paperclip/tree/main/app/wps)，本仓库做了 Surge 模块封装：加了图形化参数（含「关闭 Cookie 抓取」开关），其余逻辑保持上游原样。

## 安装

模块地址：

```
https://raw.githubusercontent.com/lylywayr/Surge/Master/Module/Spec/WPSCheckin/WPSCheckin.sgmodule
```

Surge → 首页 → 模块 → 右上角 + → 粘贴地址 → 安装。安装后点模块右侧的「参数」即可调整（需要 Surge iOS 5.10+）。

## 参数说明

| 参数 | 默认 | 说明 |
|---|---|---|
| `cookie` | `true` | 是否自动抓取 `wps_sid`。抓到一次后填 `false`，就不再拦截 WPS 请求 |
| `hour` / `minute` | `10` / `0` | 每日执行时间，默认 10:00（活动 10 点开抢，别改早） |
| `hot` | `true` | 限量爆款领取（每天限 1 次，库存少，排在第一个执行） |
| `trial` | `true` | 会员试用申请（7 天 / 月卡 / 3 个月卡三档全申领，次日开奖） |
| `signin` | `true` | 每日签到 |
| `fragment` | `true` | 福利中心连续打卡领会员 |
| `lottery` | `true` | 天天抽奖（免费次数 10:00 后才刷新） |
| `clockin` | `true` | 小程序每日打卡 |
| `merge` | `false` | 合并通知：`true` 时不在任务里单独弹通知，只留最后的任务汇总 |
| `debug` | `false` | 打印接口原始响应，排错用 |

布尔参数填 `true` / `false`；所有任务默认全开，关掉的整项跳过（连同它的动作间隔）。

## 首次抓 Cookie

1. 安装模块并确认「MITM」总开关已打开、证书已安装信任（`personal-act.wps.cn` 已由模块自动加入 MITM 主机名）
2. 打开 WPS App → 进入任意活动页（任务中心 / 福利中心「天天领福利」）停留 1 秒
3. 收到 `✅ WPS Cookie 获取成功` 即成功
4. 之后可把参数 `cookie` 改成 `false`，把抓取脚本关掉（模块已存的 `wps_sid` 不受影响）

> 只抓 `wps_sid` 这一个长效登录态，不轮换，抓一次长期可用；只有手动退登 / 改密 / 异地风控才需要重抓。账号信息运行时动态获取，脚本不保存任何个人信息。

## 工作方式

- 每天 `hour:minute`（默认 10:00）由 `wake-system` 到点唤醒执行，`timeout=120` 秒
- 先抢限量爆款，再依次做签到、打卡、抽奖、试用、小程序打卡
- 任务串行、动作之间随机间隔几秒（模拟真人，避风控），不并发
- 结束时推一条任务汇总通知，`merge=true` 时只保留这条

## 文件

| 文件 | 说明 |
|---|---|
| `WPSCheckin.sgmodule` | Surge 模块（MITM + 两个脚本入口 + 参数表） |
| `wps.js` | 签到主脚本（上游副本 + 参数适配段，标记为 `Surge 模块参数适配`） |
| `wps.cookie.js` | Cookie 抓取脚本（上游副本 + `cookie` 开关） |

## 维护 / 更新上游

两个脚本是与上游同步的副本，只在顶部加了 `Surge 模块参数适配` / `Surge 模块参数` 段落。上游更新时：重新拉取 `MaYIHEI/paperclip` 的 `app/wps/wps.js`、`wps.cookie.js`，把对应补丁段合并回来即可；实在不会合并就直接用上游原版，此时模块参数里的任务开关会失效（Cookie 开关仍建议保留，否则需要手改 `script-path`）。

## 注意

- 活动配置（`component_number` 等）与具体活动期绑定，上游换期后需同步更新 `wps.js` 顶部常量，否则福利中心任务会报错
- 限量爆款界面是「任选 1 个」，脚本只领其中一项
- 纯 Surge 脚本实现，无外部依赖
