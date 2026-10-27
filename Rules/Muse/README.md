# Muse (Meta) 分流规则

Meta 于 2026-09-08 上线的个人 AI Agent，官网 <https://muse.ai>（TLS 证书 `O=Meta Platforms, Inc.` / `CN=*.muse.ai`）。
截至 2026-10 仅**美国 / 加拿大**开放，策略组请指向美区节点，并保持 DNS 远端解析。

## 文件

| 文件 | 适用客户端 |
| --- | --- |
| `Muse.list` | Surge / Stash / Loon / Quantumult X / Shadowrocket |
| `Muse-egern.yaml` | Egern（原生 `domain_suffix_set`） |
| `Muse.yaml` | Clash / Mihomo（classical `payload`） |

三份内容一致，共 13 条：`muse.ai` 本体 + Meta AI 后端/内容 CDN + 登录与媒体依赖（Instagram / Facebook 基建）。

## 用法

### Surge

```ini
[Rule]
RULE-SET,https://raw.githubusercontent.com/lylywayr/Surge/Master/Rules/Muse/Muse.list,Muse

[Proxy Group]
Muse = url-test, policy-regex-filter=(?i)US|美, interval=600
```

### Egern

```yaml
rules:
  - rule_set: match: "https://raw.githubusercontent.com/lylywayr/Surge/Master/Rules/Muse/Muse-egern.yaml"
    policy: Muse
    update_interval: 86400
```

### Clash / Mihomo

```yaml
rule-providers:
  muse:
    type: http
    behavior: classical
    format: yaml
    url: "https://raw.githubusercontent.com/lylywayr/Surge/Master/Rules/Muse/Muse.yaml"
    path: ./Muse.yaml
    interval: 86400

rules:
  - RULE-SET,muse,Muse
```

## 注意

- `facebook.com` / `instagram.com` / `fbcdn.net` 等属于登录与媒体依赖。若配置中已有独立分组的 Facebook、Instagram 规则，可删除文件末尾“可选”段落，避免与已有分组抢流量。
- `meta-agents-apps.workers.dev` 是独立的 Worker 子域，请勿写成整个 `workers.dev`。
- 规则放在最终兜底（`FINAL` / `MATCH`）之前。

## 上游参考

- <https://github.com/VPSDance/ai-proxy-rules>（按服务拆分，含 `meta-ai.*`）
- <https://github.com/jinxinkai/clash-ai-rules>（`Muse.yaml`）
- <https://github.com/Rabbit-Spec/Surge>（`Rules/Proxy.list`）
