# 网易严选签到（Surge 模块）

网易严选活动页每日自动签到，领严选积分。

脚本来自 [ddgksf2013/Scripts](https://github.com/ddgksf2013/Scripts/blob/master/yanxuan_daily_sign.js)，本仓库做了 Surge 模块封装：加了图形化参数（含「关闭 Cookie 抓取」开关），签到逻辑保持上游原样。

## 安装

模块地址：

```
https://raw.githubusercontent.com/lylywayr/Surge/Master/Module/Spec/YanxuanCheckin/YanxuanCheckin.sgmodule
```

Surge → 首页 → 模块 → 右上角 + → 粘贴地址 → 安装。参数表需要 Surge iOS 5.10+ / Mac 5.6+。

## 参数说明

| 参数 | 默认 | 说明 |
|---|---|---|
| `cookie` | `true` | 是否自动抓取登录态。抓到一次后填 `false`，抓取脚本就只放行、不再读写登录态 |
| `hour` / `minute` | `9` / `10` | 每日签到时间，默认 09:10 |
| `notify` | `true` | 是否推送通知；填 `false` 只写日志 |

布尔参数填 `true` / `false`。

> `cookie=false` 只关闭 http-request 抓取，定时签到照常运行，已存的登录态不受影响。

## 首次抓登录态

1. 安装模块，确认 MITM 总开关已开、证书已安装并信任（`act.you.163.com` 由模块自动追加到 MITM 主机名）
2. 手机上登录网易严选 App / H5
3. 打开签到活动页：

```
https://act.you.163.com/act/pub/ssr/HysmFsmeKj88.html?appConfig=1_1_2
```

4. 页面发出 `task/list` / `att/v4/index` / `att/v3/sign` 请求时自动抓取，收到「Cookie 抓取成功」通知即完成
5. 之后可把参数 `cookie` 改成 `false` 关掉抓取

抓到的内容是本机登录态（`Cookie` + `User-Agent`），存在 Surge 的持久化存储 `yx_attendance_own_session_v1` 里，只用于签到请求。

## 工作方式

- 每天 `hour:minute`（默认 09:10）由 `wake-system` 到点唤醒执行，`timeout=60` 秒
- 先请求活动初始化刷新 `Set-Cookie`，查签到状态 → 调签到接口 → 再查一次积分复核
- 结束时推一条结果通知（签到成功 / 今日已签到 / 登录态失效等）

## 文件

| 文件 | 说明 |
|---|---|
| `YanxuanCheckin.sgmodule` | Surge 模块（MITM + 抓取/定时两个脚本入口 + 参数表） |
| `yanxuan_daily_sign.js` | 上游脚本副本（压缩代码），顶部加「Surge 模块参数适配」段 |

## 维护 / 更新上游

`yanxuan_daily_sign.js` 是上游压缩脚本的镜像，只在开头加了参数解析段、给 `yxNotify` 加了通知开关、在末尾分发处加了 `cookie=false` 判断。上游更新后重新拉取，按这三处补丁合并即可；不合并也能用，但模块参数（含 Cookie 开关）会失效。

## 注意

- 登录态失效（手动退出、改密、风控）后需重新打开活动页抓一次
- 本模块只做签到，不处理严选的其它活动任务
- MITM 使用 `%APPEND%`，不会覆盖主配置里已有的主机名
