# ACL4MerlinClash

![Sync Providers](https://github.com/avman-cn/ACL4MerlinClash/actions/workflows/sync_providers.yml/badge.svg)

Clash 规则集（Rule Providers）仓库。内容自动同步自 [ACL4SSR](https://github.com/ACL4SSR/ACL4SSR) 的 `Clash/Providers`，并通过 jsDelivr CDN 分发，可直接作为 Clash / Clash.Meta（mihomo）的 `rule-providers` 使用。

## 特性

- **自动同步**：GitHub Actions 每 12 小时从上游拉取一次最新规则
- **自动提交**：检测到变更时自动 commit & push（提交者为 `github-actions[bot]`）
- **自动清缓存**：推送成功后自动清除变更文件在 jsDelivr 上的缓存，保证 CDN 内容及时更新
- **开箱即用**：所有文件均为标准 Clash `payload:` 格式

## 目录结构

```text
.
├── *.yaml                     # 顶层常用聚合规则集
├── Ruleset/                   # 按应用/服务细分的规则集
│   ├── *.yaml
│   └── Other/                 # 补充规则
└── .github/workflows/
    └── sync_providers.yml     # 自动同步工作流
```

## 规则文件

### 顶层聚合规则集

| 文件 | 说明 |
| --- | --- |
| `Apple.yaml` | Apple 相关域名 |
| `BanAD.yaml` | 广告域名拦截 |
| `BanEasyList.yaml` | EasyList 广告规则 |
| `BanEasyListChina.yaml` | EasyList China 中国区广告规则 |
| `BanEasyPrivacy.yaml` | EasyPrivacy 隐私追踪拦截 |
| `BanProgramAD.yaml` | 应用程序内广告 |
| `ChinaCompanyIp.yaml` | 国内公司 IP 段 |
| `ChinaDomain.yaml` | 国内域名（直连） |
| `ChinaIp.yaml` | 中国 IP 地址段（IPv4） |
| `ChinaIpV6.yaml` | 中国 IP 地址段（IPv6） |
| `ChinaMedia.yaml` | 国内流媒体 |
| `Download.yaml` | 下载 / BT 类 |
| `LocalAreaNetwork.yaml` | 局域网直连 |
| `MJJ.yaml` | 主机 / VPS 论坛类 |
| `ProxyGFWlist.yaml` | GFW 黑名单（需代理） |
| `ProxyLite.yaml` | 精简代理列表 |
| `ProxyMedia.yaml` | 国外流媒体（需代理） |
| `UnBan.yaml` | 解除屏蔽 / 直连修正 |

### 细分规则集

`Ruleset/` 目录下为按应用或服务细分的规则集，例如 `Google.yaml`、`Netflix.yaml`、`YouTube.yaml`、`Spotify.yaml`、`OpenAi.yaml`、`Telegram.yaml` 等，覆盖流媒体、游戏、社交、开发工具、AI 服务等场景；`Ruleset/Other/` 下为补充规则（如 `ChatGPT.yaml`、`Games.yaml`、`Direct.yaml`、`ProxyMedia.yaml` 等）。

> **关于 behavior**：绝大多数文件为 `classical` 行为（条目自带 `DOMAIN-SUFFIX,`、`IP-CIDR,` 等类型前缀）；`ChinaIp.yaml`、`ChinaIpV6.yaml`、`ChinaCompanyIp.yaml` 等纯 IP 段文件为 `ipcidr` 行为。引用时请按文件实际内容填写。

## 通过 CDN 访问

源地址（GitHub raw，国内可能不稳定）：

```text
https://raw.githubusercontent.com/avman-cn/ACL4MerlinClash/main/<文件路径>
```

推荐使用 jsDelivr 加速：

```text
https://cdn.jsdelivr.net/gh/avman-cn/ACL4MerlinClash@main/<文件路径>
```

例如：

```text
https://cdn.jsdelivr.net/gh/avman-cn/ACL4MerlinClash@main/ChinaDomain.yaml
https://cdn.jsdelivr.net/gh/avman-cn/ACL4MerlinClash@main/Ruleset/Google.yaml
```

- `@main` 跟随最新内容（缓存由工作流自动清除）；也可用具体 commit SHA 锁定版本：`@<commit-sha>`
- 备用节点：`https://fastly.jsdelivr.net/gh/...`、`https://gcore.jsdelivr.net/gh/...`

## 在 Clash 中使用

在配置文件的 `rule-providers` 中引用：

```yaml
rule-providers:
  ChinaDomain:
    type: http
    behavior: classical
    format: yaml
    url: "https://cdn.jsdelivr.net/gh/avman-cn/ACL4MerlinClash@main/ChinaDomain.yaml"
    path: ./ruleset/ChinaDomain.yaml
    interval: 86400

  Google:
    type: http
    behavior: classical
    format: yaml
    url: "https://cdn.jsdelivr.net/gh/avman-cn/ACL4MerlinClash@main/Ruleset/Google.yaml"
    path: ./ruleset/Google.yaml
    interval: 86400
```

随后在 `rules` 中按 `RULE-SET,ChinaDomain,直连` 之类的方式引用即可。

## 自动同步机制

工作流定义见 [`.github/workflows/sync_providers.yml`](.github/workflows/sync_providers.yml)：

1. 稀疏克隆上游 `ACL4SSR/ACL4SSR` 的 `Clash/Providers/`
2. 覆盖到本仓库根目录
3. 有变更时自动提交并推送
4. 对本次变更的文件调用 jsDelivr purge 接口清除 CDN 缓存

触发方式：定时（每 12 小时）+ 手动触发（Actions 页面 *Run workflow*）。

## 数据来源

- 规则来源：[ACL4SSR/ACL4SSR](https://github.com/ACL4SSR/ACL4SSR)
- 中国 IP 段等数据来源见各文件内注释

## 免责声明

本仓库仅作规则文件的镜像与分发之用，所有规则版权归原作者所有。请自行评估使用风险，因使用本仓库内容产生的任何后果由使用者自行承担。
