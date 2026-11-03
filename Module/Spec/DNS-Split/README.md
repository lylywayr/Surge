# DNS 分流模块

用 `[Host]` 的 `server:` 语法，给**指定域名**单独指定 DNS 服务器，实现"国内走国内 DNS、境外走加密 DNS"。

## 原理

Surge 的 `[Host]` 支持：

```
example.com = 1.2.3.4                     # 固定 IP
*.dev = 6.7.8.9                           # 通配
foo.com = server:8.8.8.8                  # 指定 DNS
bar.com = server:https://doh.pub/dns-query # 指定 DoH
```

模块里的 `[Host]` 行会**插入到 Profile 的 [Host] 最前面**（先匹配先生效），所以本模块的规则优先于配置里的同名条目。

## 覆盖范围

| 分组 | 域名 |
|---|---|
| Google / YouTube | `google.com`、`*.googleapis.com`、`*.gstatic.com`、`*.youtube.com`、`*.googlevideo.com` 等 |
| OpenAI / Anthropic | `*.openai.com`、`*.chatgpt.com`、`*.anthropic.com`、`*.claude.ai` |
| Telegram | `*.telegram.org`、`*.t.me`、`*.telegra.ph` |
| GitHub | `github.com`、`*.githubusercontent.com`、`*.githubassets.com` |
| 社交 / 媒体 | `*.twitter.com`、`*.x.com`、`*.facebook.com`、`*.instagram.com`、`*.reddit.com`、`*.discord.com` |
| 资讯 / 其他 | `*.wikipedia.org`、`*.bbc.com`、`*.nytimes.com`、`*.medium.com`、`*.proton.me`、`*.signal.org` |

## 参数

| 参数 | 默认 | 说明 |
|---|---|---|
| `doh` | `https://doh.pub/dns-query` | 境外域名使用的加密 DNS。实测腾讯 DoH 能返回真实 IP；阿里 DoH 会返回污染结果，别用 |

## 安装 / 回滚

- 安装：导入并启用
- 回滚：禁用模块即可，Profile 的 `[Host]` 立即恢复原状
- 想加域名：直接改模块文件里的 `[Host]`，一行一个，支持 `*` 通配

## 注意

- 只影响 **Surge 内部 DNS 客户端**（含增强模式的 fake-IP 响应）
- 走代理的域名本来就是远端解析，这里主要让**本地解析/直连场景**拿到干净结果
- DoH 不可达时这些域名会解析失败（没有回退），所以别把 `doh` 指到国内访问不了的地址
