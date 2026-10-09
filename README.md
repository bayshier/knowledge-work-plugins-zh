# Knowledge Work Plugins 中文版

> 本仓库是 [anthropics/knowledge-work-plugins](https://github.com/anthropics/knowledge-work-plugins)（Apache-2.0）的中文汉化版。
> 插件能让 Claude 成为你的岗位、团队与公司的领域专家。为 [Claude Cowork](https://claude.com/product/cowork) 打造，同样兼容 [Claude Code](https://claude.com/product/claude-code)。

**汉化范围说明**：插件目录层（本 README、`.claude-plugin/marketplace.json` 的 123 条插件描述、各插件 `plugin.json` 的描述）已全部中文化——这正是 Cowork / Claude Code 插件选择器里实际显示的文本。各插件的 `SKILL.md`、`references/`、`scripts/` 与 `commands/` 属于 **agent 消费层**，按上游英文原文逐字节保留，以保证插件行为与上游完全一致；安装与使用方式不变。

## 为什么需要插件

在 Cowork 里，你只需设定目标，Claude 就会交付完成的、专业级的成果。插件让你更进一步：告诉 Claude 你偏好的工作方式、从哪些工具和数据取数、如何处理关键工作流、暴露哪些斜杠命令——让你的团队获得更好、更一致的结果。

每个插件把某一职能所需的技能、连接器、斜杠命令与子代理打包在一起。开箱即用，它们已经能让 Claude 在该角色上有个很强的起点。而真正的威力在于按你的公司定制它们——你的工具、你的术语、你的流程——让 Claude 用起来就像专门为你的团队打造。

## 插件市场

我们开源了 11 个由 Anthropic 自身工作实践孕育的插件：

| 插件 | 能帮上什么 | 连接器 |
|--------|-------------|------------|
| **[productivity](./productivity)** | 管理任务、日历、日常工作流与个人上下文，让你少花时间重复自己。 | Slack、Notion、Asana、Linear、Jira、Monday、ClickUp、Microsoft 365 |
| **[sales](./sales)** | 研究潜客、准备电话、评审销售管线、起草触达、搭建竞品作战卡。 | Slack、HubSpot、Close、Clay、ZoomInfo、Notion、Jira、Fireflies、Microsoft 365 |
| **[customer-support](./customer-support)** | 分诊工单、起草回复、打包升级、研究客户上下文，把已解决的问题变成知识库文章。 | Slack、Intercom、HubSpot、Guru、Jira、Notion、Microsoft 365 |
| **[product-management](./product-management)** | 撰写规格、规划路线图、综合用户研究、同步利益相关者、追踪竞争格局。 | Slack、Linear、Asana、Monday、ClickUp、Jira、Notion、Figma、Amplitude、Pendo、Intercom、Fireflies |
| **[marketing](./marketing)** | 起草内容、策划活动、贯彻品牌语调、竞品简报、跨渠道汇报表现。 | Slack、Canva、Figma、HubSpot、Amplitude、Notion、Ahrefs、SimilarWeb、Klaviyo |
| **[legal](./legal)** | 审查合同、NDA 分诊、合规导航、风险评估、会议准备、起草模板化回复。 | Slack、Box、Egnyte、Jira、Microsoft 365 |
| **[finance](./finance)** | 准备日记账分录、对账、生成财务报表、差异分析、管理结账、支持审计。 | Snowflake、Databricks、BigQuery、Slack、Microsoft 365 |
| **[data](./data)** | 查询、可视化并解读数据集——写 SQL、跑统计分析、搭仪表盘，分享之前先验证。 | Snowflake、Databricks、BigQuery、Definite、Hex、Amplitude、Jira |
| **[enterprise-search](./enterprise-search)** | 跨邮件、聊天、文档与 wiki 找到任何东西——一条查询覆盖公司全部工具。 | Slack、Notion、Guru、Jira、Asana、Microsoft 365 |
| **[bio-research](./bio-research)** | 连接临床前研究工具与数据库（文献检索、基因组学分析、靶点优先级排序），加速早期生命科学研发。 | PubMed、BioRender、bioRxiv、ClinicalTrials.gov、ChEMBL、Synapse、Wiley、Owkin、Open Targets、Benchling |
| **[cowork-plugin-management](./cowork-plugin-management)** | 为你组织的特定工具与工作流创建新插件或定制现有插件。 | — |

另外，市场里还有 112 个来自合作伙伴的插件（`partner-built/`，含 Noibu、Zapier、Figma、Cloudinary、Datadog、Canva 等），目录描述同样已汉化。

安装可直接在 Cowork 中进行，也可以在这里浏览完整合集，或者构建你自己的插件。

## 快速开始

### Cowork

从 [claude.com/plugins](https://claude.com/plugins/) 安装插件。

### Claude Code

```bash
# 先添加市场
claude plugin marketplace add anthropics/knowledge-work-plugins

# 再安装指定插件
claude plugin install sales@knowledge-work-plugins
```

安装后插件自动生效：技能在相关场景自动触发，斜杠命令在会话中可用（例如 `/sales:call-prep`、`/data:write-query`）。

> 注：本汉化仓库保留了完整的上游结构，同样可以作为市场添加：
> `claude plugin marketplace add bayshier/knowledge-work-plugins-zh`。

## 插件的工作原理

每个插件都遵循同一结构：

```
plugin-name/
├── .claude-plugin/plugin.json   # 清单
├── .mcp.json                    # 工具连接
├── commands/                    # 你显式调用的斜杠命令
└── skills/                      # Claude 自动调用的领域知识
```

- **技能（Skills）** 编码 Claude 提供有用帮助所需的领域专长、最佳实践与分步工作流。相关场景下 Claude 自动调用。
- **命令（Commands）** 是你显式触发的动作（例如 `/finance:reconciliation`、`/product-management:write-spec`）。
- **连接器（Connectors）** 通过 [MCP 服务器](https://modelcontextprotocol.io/) 把 Claude 接到你岗位依赖的外部工具——CRM、项目追踪器、数据仓库、设计工具等等。

所有组件都是基于文件的——markdown 与 JSON，没有代码、没有基础设施、没有构建步骤。

## 让插件成为你自己的

这些插件是通用的起点。按你公司的真实运作方式定制它们，价值会大得多：

- **更换连接器** — 编辑 `.mcp.json`，指向你自己的工具栈。
- **补充公司上下文** — 把你的术语、组织结构与流程写进技能文件，让 Claude 理解你的世界。
- **调整工作流** — 按你团队实际的做事方式修改技能指令，而不是教科书式的方法。
- **构建新插件** — 用 `cowork-plugin-management` 插件，或按上面的结构为尚未覆盖的职能与工作流创建插件。

当你的团队构建并分享插件，Claude 会成为跨职能专家。你定义的上下文会融入每一次相关交互，让管理者和行政人员把时间花在改进流程上，而不是监督流程上。

## 贡献

插件就是 markdown 文件。Fork 本仓库、做修改、提交 PR。

> 本中文仓库的上游同步说明：目录层译文随上游更新逐步跟进；
> agent 消费层（SKILL.md 等）保持与上游同版本（commit `ae1513e` 基线）。

## 许可

Apache-2.0（见 [LICENSE](LICENSE)），与上游一致。翻译部分按同等许可发布。
