# neo-oen/rule

Clash / Mihomo / Sing-box 分流规则补充列表（个人维护）。

Fork 自 [qichiyuhub/rule](https://github.com/qichiyuhub/rule)，按自己环境裁剪。

## 文件说明

| 文件 | 用途 |
|------|------|
| `direct.list` | 强制 **直连** 的域名（内网、走代理 502 的站等） |
| `proxy.list` | 强制走 **代理** 的域名（订阅未覆盖、DNS 等） |

## Mihomo / Clash 用法

在 `rule-providers` 中：

```yaml
rule-anchor:
  class: &class {type: http, interval: 86400, behavior: classical, format: text}

rule-providers:
  directlite: { <<: *class, url: "https://raw.githubusercontent.com/neo-oen/rule/refs/heads/main/direct.list" }
  proxylite:  { <<: *class, url: "https://raw.githubusercontent.com/neo-oen/rule/refs/heads/main/proxy.list" }
```

在 `rules` 中（**顺序重要**）：

```yaml
rules:
  - RULE-SET,private_ip,直连,no-resolve
  - RULE-SET,private_domain,直连
  - RULE-SET,directlite,直连          # 必须在 geolocation-!cn 之前
  - RULE-SET,proxylite,🚀 默认代理
  # ... 其他规则集 ...
  - RULE-SET,geolocation-!cn,🚀 默认代理
  - MATCH,🐟 漏网之鱼
```

### 注意（TUN 模式）

- 规则里的 **「直连」** = Clash 直连出站，**不是** 关闭 TUN。
- 内网域名除 `direct.list` 外，建议在主配置保留 `nameserver-policy`、`fake-ip-filter`、`sniffer.skip-domain`（如 `mirrorfs.com`、`hf.haier`）。

## 维护

- 需直连：编辑 `direct.list`，提交 push，Clash Verge 中更新配置。
- 需代理：编辑 `proxy.list`，同上。
