---
title: 使用 Commvault 将数据备份到 Azure
titleSuffix: Azure Storage
description: 概述将 Azure 用作 Commvault 完整备份与恢复的存储目标和恢复位置时要考虑的因素以及要遵循的步骤
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 8d897c4e4c76b42e980c23f1f18789248f04909a
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129270498"
---
# <a name="backup-to-azure-with-commvault"></a>使用 Commvault 备份到 Azure

本文帮助你将 Commvault 基础结构与 Azure Blob 存储集成。 内容包括先决条件、注意事项、实施指导和操作指导。 本文介绍在发生了导致主站点无法正常运营的灾难时，如何使用 Azure 作为场外备份目标和恢复站点。

> [!NOTE]
> Commvault 提供一种较低恢复时间目标 (RTO) 解决方案，即 Commvault Live Sync。此解决方案可让你拥有一个备用 VM，可帮助你在 Azure 生产环境中发生灾难时更快地恢复。 这些功能不属于本文档的介绍范围。

## <a name="reference-architecture"></a>参考体系结构

下图提供了本地到 Azure 的部署以及 Azure 内部部署的参考体系结构。

![Commvault 到 Azure 部署的参考体系结构](../media/commvault-diagram.png)

现有的 Commvault 部署可以通过将一个或多个 Azure 存储帐户添加为云存储目标来轻松与 Azure 集成。 Commvault 还允许在 Azure 内恢复本地备份，这相当于你在 Azure 中拥有一个按需恢复站点。

## <a name="commvault-interoperability-matrix"></a>Commvault 互操作性矩阵

| 工作负荷 | GPv2 和 Blob 存储 | 冷层支持 | 存档层支持 | Data Box 系列支持 |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| 本地 VM/数据 | v11.5 | v11.5 | v11.10 | v11.10 |
| Azure VM | v11.5 | v11.5 | v11.5 | 不可用 |
| Azure Blob | v11.6 | v11.6 | v11.6 | 不可用 |
| Azure 文件 | v11.6 | v11.6 | v11.6 | 不可用 |

## <a name="before-you-begin"></a>开始之前

稍微提前规划一下就有助于将 Azure 用作场外备份目标和恢复站点。

### <a name="get-started-with-azure"></a>Azure 入门

Microsoft 提供一个框架来帮助你开始使用 Azure。 [云采用框架](/azure/architecture/cloud-adoption/) (CAF) 提供实现企业数字变革的详细措施，以及有关规划生产级云采用方案的综合指导。 CAF 包含循序渐进的 [Azure 设置指南](/azure/cloud-adoption-framework/ready/azure-setup-guide/)，帮助你快速安全地开始运作。 可以在 [Azure 门户](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)中找到交互式版本。 在其中可以找到示例体系结构、有关部署应用程序的具体最佳做法，以及用于将你从新手培养成为 Azure 专家的免费培训资源。

### <a name="consider-the-network-between-your-location-and-azure"></a>考虑你所在位置与 Azure 之间的网络

不管是使用云资源来进行生产、测试和开发，还是将其用作备份目标和恢复站点，都必须了解初始备份种子设定和日常数据传输的带宽需求。

Azure Data Box 提供了一种无需更多带宽即可将初始备份基线传输到 Azure 的方式。 这在预计的基线传输所需时间超过了你的容忍范围时会很有用。 创建存储帐户时，可以使用数据传输估算器来估算传输初始备份所需的时间。

![显示门户中的 Azure 存储数据传输估算器。](../media/az-storage-transfer.png)

请记住，需有足够的网络容量才能在不影响生产应用程序的情况下，在所需的传输期限（备份期限）内支持每日数据传输。 本部分概述了可用于评估网络需求的工具和方法。

#### <a name="determine-how-much-bandwidth-youll-need"></a>确定需要多大的带宽

如果要确定需要的带宽大小，请使用以下资源：

- 来自备份软件的报表。
- Commvault 提供标准报表，用于确定将初始基线传输到 Azure 的[更改速率](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm)和[备份集总大小](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm)。
- 独立于备份软件的评估和报告工具，例如：
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>确定未利用的 Internet 带宽

必须知道在日常中通常有多少带宽不被利用到（即“空余空间”）。 这有助于评估是否可以满足以下方面的目标：

- 不使用 Azure Data Box 进行脱机种子设定时的初始上传时间
- 根据以前确定的更改率和备份期限完成日常备份

使用以下方法来确定备份到 Azure 时可任意占用的带宽空余空间。

- 如果你是现有的 Azure ExpressRoute 客户，请在 Azure 门户中查看[线路用量](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics)。
- 联系你的 ISP。 他们应该可以向你分享报告，指明你的现有每日和每月使用量。
- 有多个工具可以通过监视路由器/交换机级别的网络流量来测量使用量。 其中包括:
  - [Solarwinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>选择适当的存储选项

使用 Azure 作为备份目标时，将会使用 [Azure Blob 存储](../../../../blobs/storage-blobs-introduction.md)。 Blob 存储是 Microsoft 的对象存储解决方案。 Blob 存储经过优化，可存储海量的非结构化数据（即，不遵循任何数据模型或定义的数据）。 此外，Azure 存储非常持久、高度可用、安全且可缩放。 你可以为工作负载选择适当的存储，以提供满足内部 SLA 的[复原级别](../../../../common/storage-redundancy.md)。 Blob 存储是按用量付费的服务。 它按照存储的数据量、访问这些数据的次数，以及选择冷层和存档层时所需的最短保留期[按月收费](../../../../blobs/access-tiers-overview.md#pricing-and-billing)。 下表汇总了适用于备份数据的复原和分层选项。

**Blob 存储复原选项：**

|  |本地冗余  |区域冗余  |地理冗余  |异地区域冗余  |
|---------|---------|---------|---------|---------|
|**有效副本数**     | 3         | 3         | 6         | 6 |
|**可用性区域数**     | 1         | 3         | 2         | 4 |
|**区域数**     | 1         | 1         | 2         | 2 |
|**手动故障转移到次要区域**     | 不可用         | 不可用         | 是         | 是 |

**Blob 存储层：**

|  | 热层   |冷层   | 存档层 |
| ----------- | ----------- | -----------  | -----------  |
| **可用性** | 99.9%         | 99%         | Offline      |
| 使用费 | 存储费用较高，访问和事务费用较低 | 存储费用较低，访问和事务费用较高 | 存储费用最低，访问和事务费用最高 |
| **所需的最短数据保留期**| 不可用 | 30 天 | 180 天 |
| **延迟（距收到第一个字节的时间）** | 毫秒 | 毫秒 | 小时 |

#### <a name="sample-backup-to-azure-cost-model"></a>备份到 Azure 成本模型的示例

对于不熟悉云的客户而言，按用量付费可能令人望而生畏。 尽管你只需为使用的容量付费，但使用 [Azure Express Route Direct Local 或 Express Route 不限流量套餐](https://azure.microsoft.com/pricing/details/expressroute/)（包括从 Azure 流出的数据）时，确实还要支付事务（读取或写入）费，以及读回到本地环境的[数据的流出](https://azure.microsoft.com/pricing/details/bandwidth/)费。 可以使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)来执行“假设”(what-if) 分析。 可将分析建立在价目表定价或 [Azure 存储预留容量定价](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)的基础之上，这样可以节省高达 38% 的成本。 以下示例定价练习为备份到 Azure 的每月成本建模。 这只是一个示例。 由于此处未捕获到某些活动，你的定价可能有所不同。

|成本因素  |每月成本  |
|---------|---------|
|冷存储中的 100 TB 备份数据     |$1556.48         |
|每天写入的 2 TB 新数据 x 30 天     |$39 事务成本          |
|每月估算总计     |$1595.48         |
|---------|---------|
|通过公共 Internet 一次性将 5 TB 还原到本地   | $491.26         |

> [!NOTE]
> 这项成本估算是使用美国东部区域的即用即付定价，根据每天生成 65,536 个 PUT 请求（写入事务）的 Commvault 默认子区块大小（32 MB），在 Azure 定价计算器中生成的。 此示例可能不适合你的要求。

## <a name="implementation-guidance"></a>实施指南

本部分提供有关如何将 Azure 存储添加到本地 Commvault 部署的简要指导。 详细的指导和规划注意事项请参阅 [Microsoft Azure 的 Commvault 公有云体系结构指南](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)。

1. 打开 Azure 门户，并搜索“存储帐户”。 还可以单击默认的“存储帐户”图标。

    ![展示如何在 Azure 门户中添加存储帐户。](../media/azure-portal.png)
  
    ![展示在 Azure 门户的搜索框中键入存储的位置。](../media/locate-storage-account.png)

2. 选择“创建”以添加帐户。 选择或创建一个资源组，提供唯一的名称，选择区域，选择“标准”性能，始终将帐户类型保留为“存储 V2”，选择符合 SLA 的复制级别，以及备份软件将要应用的默认层。  Azure 存储帐户在单个帐户中提供热层、冷层和存档层。通过 Commvault 策略，可以使用多个层来有效管理数据的生命周期。

    ![展示门户中的存储帐户设置](../media/account-create-1.png)

3. 暂时保留默认网络选项并转到“数据保护”。 在此处可以选择启用软删除，这样就可以在定义的保留期内恢复意外删除的备份文件，并针对意外删除或恶意删除提供保护。

    ![展示门户中的“数据保护”设置。](../media/account-create-2.png)

4. 接下来，对于备份到 Azure 的用例，我们建议使用“高级”屏幕中的默认设置。

    ![展示门户中的“高级”设置选项卡。](../media/account-create-3.png)

5. 为组织添加标记（如果使用标记功能），并创建帐户。

6. 现在只需完成两个快速步骤，就可以将帐户添加到 Commvault 环境。 在 Azure 门户中导航到你创建的帐户，然后在“Blob 服务”菜单下选择“容器”。  添加一个容器并选择有意义的名称。 然后，导航到“设置”下的“访问密钥”项，复制“存储帐户名称”和两个访问密钥中的一个。   在后续步骤中，需要用到容器名称、帐户名称和访问密钥。

    ![展示如何在门户中创建容器。](../media/container.png)

    ![展示门户中的访问密钥设置。](../media/access-key.png)

7. （可选）可将更多安全层添加到部署。

    1. 配置基于角色的访问，以限制可对你的存储帐户进行更改的人员。 有关详细信息，请参阅[用于管理操作的内置角色](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)。
    1. 使用[存储防火墙设置](../../../../common/storage-network-security.md)来限制只能通过特定的网段访问帐户，以防止有人试图从企业网络外部进行访问。

        ![展示门户中的存储防火墙设置。](../media/storage-firewall.png)

    1. 对帐户设置[删除锁](../../../../../azure-resource-manager/management/lock-resources.md)，以防止意外删除存储帐户。

        ![显示如何在门户中设置删除锁。](../media/resource-lock.png)

    1. 配置附加[安全性的最佳做法](../../../../../storage/blobs/security-recommendations.md)。

1. 在 Commvault 命令中心，导航到“管理” -> “安全” -> “凭据管理器”  。 选择“云帐户”，将 Microsoft Azure 存储选为供应商类型，选择用于同 Azure 传输数据的 MediaAgent，然后添加存储帐户名称和访问密钥   。

    ![显示如何在 Commvault 命令中心添加凭据。](../media/commvault-credential.png)

9. 接下来，在 Commvault 命令中心导航到“存储” -> “云” 。 选择“添加”。 输入存储帐户的易记名称，然后在“类型”列表中选择“Microsoft Azure 存储” 。 选择用于将备份传输到 Azure 存储的媒体代理服务器。 添加已创建的容器，选择要在 Azure 存储帐户中使用的存储层，然后选择在第 8 步中创建的凭据。 最后，选择是否传输已删除重复数据的备份，并选择已删除重复数据的数据库的位置。

     ![Commvault 的“添加云”用户界面的屏幕截图。 在存档下拉菜单中选中了“存档”。](../media/commvault-add-storage.png)

10. 最后，在 Commvault 命令中心通过“管理” -> “计划”，将你的新 Azure 存储资源作为备份目标添加到现有计划或新计划 。

    ![Commvault 命令中心用户界面的屏幕截图。 在左侧导航栏中，选中了位于“管理”之下的“计划”。](../media/commvault-plan.png)

11. （可选）如果计划将 Azure 用作恢复站点或使用 Commvault 将服务器和应用程序迁移到 Azure，最佳做法是在 Azure 中部署 VSA 代理。 可在[此处](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)找到详细说明。

## <a name="operational-guidance"></a>操作指南

### <a name="azure-alerts-and-performance-monitoring"></a>Azure 警报和性能监视

建议监视 Azure 资源和 Commvault 的功能，以像使用存储备份所依赖的任何存储目标一样使用它们。 结合使用 Azure Monitor 和 Commvault 命令中心进行监视有助于使环境保持正常状态。

#### <a name="azure-portal"></a>Azure 门户

Azure 以 [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md) 的形式提供可靠的监视解决方案。 可以[配置 Azure Monitor](../../../../blobs/monitor-blob-storage.md) 来跟踪 Azure 存储容量、事务、可用性、身份验证等。 可在[此处](../../../../blobs/monitor-blob-storage-reference.md)找到收集的指标的完整参考信息。 可跟踪的几个有用指标包括：Blobcapacity - 确保不会超过最大[存储帐户容量限制](../../../../common/scalability-targets-standard-account.md)；Ingress 和 Egress - 跟踪向/从 Azure 存储帐户写入/读取的数据量；SuccessE2ELatency - 跟踪向/从 Azure 存储和 MediaAgent 发出/接收请求的往返时间。

还可以[创建日志警报](../../../../../service-health/alerts-activity-log-service-notifications-portal.md)来跟踪 Azure 存储服务的运行状况，并随时查看 [Azure 状态仪表板](https://status.azure.com/status)。

#### <a name="commvault-command-center"></a>Commvault 命令中心

- [为云存储池创建警报](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- 如果需要了解可在哪里查看性能报表和作业完成情况以及从哪里开始基本故障排除，请参阅[仪表板](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm)。

### <a name="how-to-open-support-cases"></a>如何提出支持案例

在备份到 Azure 解决方案过程中需要帮助时，应该向 Commvault 和 Azure 两方面提出案例。 这有助于我们的支持单位在必要时展开协作。

#### <a name="to-open-a-case-with-commvault"></a>向 Commvault 提出案例

在 [Commvault 支持网站](https://www.commvault.com/support)上登录，然后提出案例。

若要了解提供的支持合同选项，请参阅 [Commvault 支持选项](https://ma.commvault.com/support)

你还可以通过电话或电子邮件联系 Commvault 支持部门提出案例：

- 免费电话：+1 877-780-3077
- [全球支持电话号码](https://ma.commvault.com/Support/TelephoneSupport)
- [Commvault 支持电子邮件地址](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>若要向 Azure 提出案例

在 [Azure 门户](https://portal.azure.com)的顶部搜索栏中搜索“支持”。 选择“帮助 + 支持” -> “新建支持请求” 。

> [!NOTE]
> 提交案例时，明确指出你需要 Azure 存储或 Azure 网络方面的帮助。 请勿指定 Azure 备份。 Azure 备份是 Azure 服务的名称，如果指定，你的案例将移交到错误的人员。

### <a name="links-to-relevant-commvault-documentation"></a>相关 Commvault 文档的链接

请参阅以下 Commvault 文档，获取更详细的信息：

- [Commvault 用户指南](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Commvault Azure 体系结构指南](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)

### <a name="marketplace-offerings"></a>市场产品/服务

Commvault 可以方便客户在 Azure 中部署解决方案以保护 Azure 虚拟机和其他许多 Azure 服务。 有关详细信息，请参阅以下资源：

- [通过 Azure 市场部署 Commvault](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Commvault for Azure 数据表](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Commvault 支持的 Azure 功能和服务的列表](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [如何使用 Commvault 保护 Azure 中的 SAP HANA](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>后续步骤

有关专业使用方案的信息，请参阅下面这些额外的 Commvault 资源。

- [使用 Commvault 将服务器和应用程序迁移到 Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [使用 Commvault 保护 Azure 中的 SAP](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [使用 Commvault 保护 Office 365](https://www.youtube.com/watch?v=dl3nvAacxZU)