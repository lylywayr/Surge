# 吾爱破解签到（Surge 模块）

把 52pojie 的 Cookie 抓取 + 每日签到封装成一个模块。签到脚本来自 [Yuheng0101/X](https://github.com/Yuheng0101/X)，抓取脚本由本仓库提供（带开关）。

## 安装

```
https://raw.githubusercontent.com/lylywayr/Surge/Master/Module/Spec/Wuai_Checkin/Wuai_Checkin.sgmodule
```

## 参数

| 参数 | 默认 | 说明 |
|---|---|---|
| `cookie` | `true` | 是否抓取 Cookie。抓到一次后设为 `false`，脚本不再写入、不再弹通知（MITM 仍会解密该域名的 HTTP 请求，但不做任何处理） |
| `hour` | `9` | 每日签到的小时（24 小时制） |
| `minute` | `0` | 每日签到的分钟 |

签到时间通过 `{{{minute}}} {{{hour}}} * * *` 注入 cron，改参数即可。

## 使用

1. 保持 `cookie = true`，用 Safari 打开 https://www.52pojie.cn/home.php 并登录（已登录则刷新一次）
2. 收到「Cookie 已获取」通知后，把模块参数 `cookie` 改成 `false`
3. 每天 `hour:minute` 自动签到；想彻底关掉解密，再禁用模块或删掉 MITM 主机名

## 实现说明

- Cookie 存在 Surge 持久化存储的 `wuai_cookie` 键里，与上游签到脚本读取的键一致，因此可以直接替换上游的抓取脚本
- 关闭开关时脚本第一行就 `$done({})`，不产生任何存储写入和通知
- 签到仍直接引用上游 `Yuheng0101/X`，上游更新自动生效
- 本模块不上传任何数据，Cookie 只留在本机
