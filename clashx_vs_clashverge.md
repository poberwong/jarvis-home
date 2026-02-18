# ClashX vs Clash Verge（区别与规则配置）

> 版本：Draft · 2026-02-18

## 一、核心区别（定位/平台）
- **ClashX**：macOS 专用客户端，定位“轻量稳定、菜单栏常驻”。
- **Clash Verge**：跨平台 GUI（Windows/macOS/Linux），功能面更完整，接近 Clash for Windows 体验。
- **内核差异**：Clash Verge 更常见使用 **Clash Meta / mihomo** 内核（更强协议与规则支持）；ClashX 传统以 Clash/ClashX 栈为主。

> 来源参考：Clash Verge 官网介绍（跨平台/GUI/协议支持）

## 二、配置方式差异
### 1) ClashX
- 以 **Profile（配置文件）** 为中心：订阅 / 本地 YAML / 自定义规则。
- 常见流程：订阅链接 → 自动更新节点 → 在 Profile 目录下修改 rules。
- 优点：轻量、简单；缺点：高级功能相对少、跨平台不便。

### 2) Clash Verge
- 以 **GUI + 内核切换** 为中心：支持多内核（Clash / Meta）。
- 配置界面更完善，可直接在 GUI 中管理规则与代理组。
- 支持更多协议与高级规则（尤其在 Meta 内核）。

## 三、规则配置方式（共通概念）
Clash 系列本质上都基于 YAML 规则分流：
- `proxies`：节点列表
- `proxy-groups`：策略组
- `rules`：匹配规则（顺序执行）
- 可选：`rule-providers`（规则集）

### 1) 直写 rules（简单方案）
```yaml
rules:
  - DOMAIN-SUFFIX,google.com,Proxy
  - DOMAIN-SUFFIX,github.com,Proxy
  - GEOIP,CN,DIRECT
  - MATCH,Proxy
```

### 2) rule-providers（更可维护）
```yaml
rule-providers:
  reject:
    type: http
    behavior: domain
    url: https://example.com/reject.list
    path: ./ruleset/reject.yaml
    interval: 86400

rules:
  - RULE-SET,reject,REJECT
  - GEOIP,CN,DIRECT
  - MATCH,Proxy
```

**差异点**：
- Clash Verge（Meta 内核）对 `rule-providers` 支持更好；
- ClashX 能用基础 `rules`，但高级规则集能力取决于内核。 

## 四、如何选
- **只用 mac + 追求稳定简单** → ClashX
- **跨平台/要更强协议与规则能力** → Clash Verge（建议 Meta 内核）
- **需要复杂规则维护** → 更推荐 Clash Verge + rule-providers

---

## 参考与来源
- Clash Verge 介绍页（跨平台/GUI/协议支持）
  - https://clashverge.org/
- ClashX 订阅/配置实践（Profile/订阅）
  - https://clashx.tech/en/article-18
