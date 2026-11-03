# DNS 分流（境外域名走代理 DoH）

给指定境外域名单独指定 Google DoH 解析，国内域名继续走本地 `dns-server`，从源头绕开 DNS 投毒。

## 为什么需要它

实测本网络：

| 解析方式 | `www.google.com` 结果 |
|---|---|
| 明文 DNS（223.5.5.5） | `185.45.5.35`（污染） |
| 腾讯 DoH **JSON 接口** | `142.251.156.119` ✅ |
| 腾讯 DoH **wire 接口**（Surge 用的就是这个） | `31.13.92.37`（还是污染） |
| 阿里 / 360 DoH wire 接口 | 同样污染 |

也就是说国内 DoH 的 wire 接口拿不到干净结果，只有**境外 DoH 经代理访问**才可靠。

## 它做了什么

```
[General]
encrypted-dns-follow-outbound-mode = true   # 让 DoH 查询跟随规则走代理

[Host]
*.google.com = server:https://dns.google/dns-query
...
```

- `encrypted-dns-follow-outbound-mode` 是必须的：DoH 默认走直连，`dns.google` 直连不可达
- `[Host]` 里的 `server:` 只影响被列出的域名，其余域名解析完全不变

## 覆盖范围

Google / YouTube、OpenAI / Anthropic、Telegram、GitHub、Twitter / X、Facebook / Instagram、Reddit、Discord、Wikipedia、BBC、NYT、Medium、Proton、Signal（共 60 余条，含裸域名与通配）

## 参数

| 参数 | 默认 | 说明 |
|---|---|---|
| `doh` | `https://dns.google/dns-query` | 可换成 `https://cloudflare-dns.com/dns-query`，两者都实测返回真实 IP |

## 验证方法

```
surge-cli dns lookup www.google.com
# server 显示 https://dns.google/dns-query，IP 是 142.251.x 即为生效
```

## 回滚

禁用模块即可，`[Host]` 与 `encrypted-dns-follow-outbound-mode` 一起回滚。

## 注意

- 这些域名的解析依赖代理：**代理不可用时它们会解析失败**（没有回退到明文 DNS）
- 首次解析多一次代理往返（约 300–800ms），之后走缓存
- 只影响 Surge 内部 DNS 客户端；走代理的连接本来就是远端解析，不受影响
- 想加域名就改 `[Host]`，一行一个，支持 `*` 通配
