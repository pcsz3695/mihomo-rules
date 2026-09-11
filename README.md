# 通用 Mihomo 分流规则

适用于中国大陆用户的通用路由模板，可由 3X-UI 合并到 Mihomo / OpenClash 订阅。更换节点名称、VPS、机场或设备时，无需修改模板中的个人信息。

通用版文件：**mihomo-routing-generic.yaml**。原 mihomo-routing-v1.yaml 保留，现有订阅不会因新增文件自动切换。

## 通用版 HTTPS 地址

https://raw.githubusercontent.com/pcsz3695/mihomo-rules/main/mihomo-routing-generic.yaml

## 分流默认值

| 流量 | 默认策略 |
|---|---|
| 局域网、私有地址及私有域名 | DIRECT |
| 中国大陆网站与 IP | Domestic → DIRECT |
| OpenAI / ChatGPT | AI → PROXY |
| Google、YouTube、GitHub、Telegram、PayPal | PROXY |
| 微软、苹果、Steam 的公开规则集所列国内服务 / 下载项 | DIRECT |
| 微软、苹果、Steam 的其他已列服务 | Microsoft / Apple / Steam → PROXY |
| 未匹配流量 | Others → PROXY |

AI 组当前覆盖 OpenAI / ChatGPT，不表示已覆盖所有 AI 厂商。AI 与 Domestic 还可动态列出主配置中直接定义的节点；通过 proxy-providers 提供的节点可经主配置的 PROXY 组选择。

PROXY 选择哪条节点，跟随它的分流组就使用哪条节点。模板不强制任何国家、机场或出口 IP。客户端保存的历史选择可能覆盖首次默认值；这份文件不会清除缓存或主动切换节点。

## 3X-UI 接入

在支持远程 Clash/Mihomo 路由的 3X-UI 中，将上面的 HTTPS 地址用于“订阅设置 → Clash / Mihomo → 全局路由规则”。字段名称以实际版本为准；这里基于 v3.7.0 的合并机制。

该字段使用一行 URL。3X-UI 负责生成实际节点与 PROXY 组，随后合并模板中的 proxy-groups、rule-providers、rules。客户端使用面板生成的完整订阅。

## 已有 Mihomo / OpenClash 配置接入

本文件只有路由部分，不能作为完整配置单独启动。主配置必须已有有效节点，以及名称大小写完全一致的 PROXY 出站组。

合并时：自定义组按名称合并或替换；rule-providers 按名称合并；rules 用本模板的有序列表替换旧列表。不要把规则追加到已有 MATCH 后面，不要创建两个同名 PROXY 组，也不要覆盖自己的节点、DNS、TUN、端口或认证配置。

如果原配置只有名为 Proxy 的组，可增加以下桥接组（将 Proxy 替换为原组的实际名称，并确认原组不反向引用 PROXY）：

~~~yaml
proxy-groups:
  - name: PROXY
    type: select
    proxies:
      - Proxy
~~~

这只是桥接示例，不是让你用它覆盖整个 proxy-groups。已有 PROXY 时无需增加。Clash Verge 的扩展配置、OpenClash 的覆写工具各有合并规则，需在生成的最终配置中确认组引用及 rules 顺序。

## 范围与依赖

- 面向支持相关字段的 Mihomo 内核；不宣称适用于所有旧版 Clash，也不是 Surge 或 sing-box 原生配置。
- 本地校验内核为 Mihomo v1.19.29。规则集采用可读 YAML，共 12 个公开 provider、25 条主规则、6 个自定义组。
- GeoSite 的 private / cn 和 GeoIP 的 private / CN 需要主配置提供相应数据文件；模板不负责安装或更换这些文件。
- 公共 provider 来自 MetaCubeX/meta-rules-dat，缓存位于 rules/routing-generic-*.yaml，每 86400 秒更新，通过 PROXY 下载。主配置须先有可用代理，否则首次下载可能失败。
- 不包含节点、订阅凭据、UUID、密码、私钥、服务器地址或客户端网络设置。
- 本版只去掉个人绑定并调整缓存文件命名，服务分类和规则顺序延续原 V1；未新增广告拦截、自动测速或链式中转。

## 验证

使用本地公开规则快照、虚构节点名称和独立目录进行 YAML / 引用 / 默认顺序检查，以及现有 Mihomo 的配置校验。覆盖单节点、改名多节点、仅代理集合供给节点三类主配置。没有用真实节点发起网络请求，没有启动常驻代理或修改正式网络。

配置校验与离线路由样例通过，不等于真实订阅、服务可用性或工作业务已经通过。公共规则集后续更新也可能改变分类。

## 参考

- [Mihomo 策略组字段与默认选择](https://wiki.metacubex.one/config/proxy-groups/)
- [3X-UI v3.7.0 Clash 路由合并源码](https://github.com/MHSanaei/3x-ui/blob/v3.7.0/internal/sub/clash_service.go)
