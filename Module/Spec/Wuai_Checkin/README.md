# 吾爱破解签到（Surge 模块）

把 52pojie 的 Cookie 抓取 + 每日签到封装成一个模块，脚本来自 [Yuheng0101/X](https://github.com/Yuheng0101/X)。

## 安装

```
https://raw.githubusercontent.com/lylywayr/Surge/Master/Module/Spec/Wuai_Checkin/Wuai_Checkin.sgmodule
```

或直接安装：

```
surge:///install-module?url=<上面地址的 percent 编码>
```

## 使用

1. 开启模块后，用 **Safari 打开 https://www.52pojie.cn/home.php 并登录**（或已登录状态刷新该页），Surge 会自动抓取 Cookie
2. 抓到后会收到通知，之后每天 09:00 自动签到
3. 抓过一次就可以把 `吾爱破解获取Cookie` 这条脚本禁用，并删掉 MITM 的 `www.52pojie.cn`，减少不必要的解密

## 参数

| 参数 | 默认 | 说明 |
|---|---|---|
| `hour` | 9 | 每日签到的小时（24 小时制） |
| `minute` | 0 | 每日签到的分钟 |

签到时间通过 `{{{minute}}} {{{hour}}} * * *` 注入 cron，改参数即可，不用改脚本。

## 说明

- 脚本不随本仓库分发，直接引用上游 `Yuheng0101/X`，上游更新会自动生效
- 若上游删除或改名，本模块会失效，届时把 `script-path` 指到 fork 即可
- Cookie 只保存在 Surge 的持久化存储里，本模块不上传任何数据
