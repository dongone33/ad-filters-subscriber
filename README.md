<div align="center">
<h1>AI-AD Filter Subscriber</h1>
  <p>
    广告过滤规则订阅器，聚合多来源、多格式的规则，自动清洗、去重、转换，帮助你快速构建属于自己的规则集~
  </p>
<!-- Badges -->
<p>
  <img src="https://img.shields.io/github/last-commit/dongone33/ad-filters-subscriber?style=flat-square" alt="last update" />
  <img src="https://img.shields.io/github/forks/dongone33/ad-filters-subscriber?style=flat-square" alt="forks" />
  <img src="https://img.shields.io/github/stars/dongone33/ad-filters-subscriber?style=flat-square" alt="stars" />
  <img src="https://img.shields.io/github/issues/dongone33/ad-filters-subscriber?style=flat-square" alt="open issues" />
  <img src="https://img.shields.io/github/license/dongone33/ad-filters-subscriber?style=flat-square" alt="license" />
</p>

<h4>
    <a href="#a">项目说明</a>
  <span> · </span>
    <a href="#b">快速开始</a>
  <span> · </span>
    <a href="#c">规则订阅</a>
  <span> · </span>
    <a href="#d">问题反馈</a>
  </h4>
</div>

<h2 id="a">📔 项目说明</h2>

**AI-AD Filter Subscriber** 是一个基于 Spring Boot与 AI 开发的广告过滤规则聚合与转换工具，核心目标是把互联网上零散的、格式各异的广告拦截规则订阅源，自动抓取、清洗、去重、格式转换后，合并输出成一份可以直接被 AdGuardHome 等 DNS 过滤软件使用的规则集。

#### 解决的问题

广告拦截规则通常分散在不同作者维护的多个订阅源里，格式也不统一（EasyList 语法、hosts 格式、DNS 专用格式等），直接把所有订阅源塞进过滤软件容易出现：
- 规则重复、体积臃肿
- 格式不兼容导致软件报错或规则失效
- 单一来源覆盖不全，拦截效果参差不齐

本项目通过统一的解析 → 转换 → 去重 → 输出流水线，把多个来源"揉"成一份干净、可直接订阅的规则文件。

#### 核心能力

- **多格式输入解析**：支持 easylist、dns (AdGuardHome)、dnsmasq、clash、smartdns、hosts 等多种规则格式作为输入源
- **规则语义转换**：自动识别规则类型（域名规则 BASIC、通配符规则 WILDCARD、正则规则 REGEX），在不同格式之间转换时尽量保留原始拦截意图
- **去重与合并**：基于哈希对规则做去重，避免不同来源间的重复规则
- **DNS 可用性探测**（可选）：对基础域名规则做 DNS 解析校验，过滤掉已失效的域名，降低误杀风险
- **黑白名单分离输出**：按 `mode`（deny/allow）、`filter`（basic/wildcard/regex）等维度灵活配置输出文件，生成独立的 Blacklist / Allowlist
- **AdGuardHome 专项适配**：正确处理 `||` 锚点、`^` 限定符、`$important`、`$badfilter`、`$denyallow` 等 AGH 专属修饰符，并将 hosts 格式重写规则转换为 AGH 的 rewrite 语法
- **全自动化更新**：配合 GitHub Actions（`.github/workflows/auto-update.yml`）定时抓取所有订阅源、重新编译生成规则文件并提交，无需人工干预

> ⚠️ 新版不再兼容原配置格式，迁移前务必注意

#### 支持的规则格式
- [x] easylist
- [x] dns (AdGuardHome)
- [x] dnsmasq
- [x] clash
- [x] smartdns
- [x] hosts

#### 注意事项
1. 仅支持基本规则转换，即域名、通配域名构成的规则，对形如 `||example.org^$popup` 等规则无法转换(合并、去重不受影响)
2. 接受不可避免的缩限，如 `||example.org^` 将拦截 example.org 及其所有子域，但将其转换为 hosts 格式时，将无法匹配子域名。
3. 规则有效性检测基于域名解析，因此仅支持基本规则 (只能检测当前域有效性，而无法检测其是否存在有效子域，故此功能可能存在误杀)。

#### 参考项目
fordes123/ad-filters-subscriber【(https://github.com/fordes123/ad-filters-subscriber)】
<h2 id="b">🛠️
