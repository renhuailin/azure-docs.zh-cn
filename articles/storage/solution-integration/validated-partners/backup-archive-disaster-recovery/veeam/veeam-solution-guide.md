---
title: 使用 Veeam 将数据备份到 Azure
titleSuffix: Azure Storage
description: 概述了使用 Azure 作为 Veeam 备份和恢复的存储目标与恢复位置时所要考虑的因素以及所要遵循的步骤
author: karauten
ms.author: karauten
ms.date: 05/12/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: e55a9d0bb21a3eded7454f74787f8be9f2f02ece
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273621"
---
# <a name="backup-to-azure-with-veeam"></a>使用 Veeam 备份到 Azure

本文帮助你将 Veeam 基础结构与 Azure Blob 存储相集成。 内容包括先决条件、注意事项、实施指导和操作指导。 本文介绍在发生了导致主站点无法正常运营的灾难时，如何使用 Azure 作为场外备份目标和恢复站点。

> [!NOTE]
> Veeam 还提供恢复时间目标 (RTO) 较低的解决方案，Veeam 备份和复制，并支持 Azure VMware 解决方案工作负荷。 使用此解决方案可以配置一个待机 VM，当 Azure 生产环境中发生灾难时，该 VM 可帮助你更快地予以恢复。 Veeam 还提供直接还原到 Microsoft Azure 和其他专用于备份 Azure 和 Office 365 资源的工具。 这些功能不属于本文档的介绍范围。

## <a name="reference-architecture"></a>参考体系结构

下图提供了本地到 Azure 的部署以及 Azure 内部部署的参考体系结构。

![Veeam 到 Azure 部署的参考体系结构示意图。](../media/veeam-architecture.png)

现有的 Veeam 部署可以通过添加一个或多个 Azure 存储帐户作为云备份存储库来轻松与 Azure 集成。 Veeam 还允许在 Azure 内从本地恢复备份，这相当于你在 Azure 中拥有一个按需恢复站点。

## <a name="veeam-interoperability-matrix"></a>Veeam 互操作性矩阵

| 工作负荷 | GPv2 和 Blob 存储 | 冷层支持 | 存档层支持 | Data Box 系列支持 |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| 本地 VM/数据 | v10a | v10a | 不可用 | 10a<sup>*</sup> |
| Azure VM | v10a | v10a | 不可用 | 10a<sup>*</sup> |
| Azure Blob | v10a | v10a | 不可用 | 10a<sup>*</sup> |
| Azure 文件 | v10a | v10a | 不可用 | 10a<sup>*</sup> |

Veeam 还为其产品的旧版本中的 Azure 功能提供支持，强烈建议使用最新的产品版本来获得最佳体验。

<sup>*</sup>对于 Azure Data Box，Veeam 备份和复制仅支持 REST API。 因此，Azure Data Box Disk 不受支持。 请参阅 [此处](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=110)，了解Data Box 支持的详细信息。


## <a name="before-you-begin"></a>开始之前

稍微提前规划一下就有助于将 Azure 用作场外备份目标和恢复站点。

### <a name="get-started-with-azure"></a>Azure 入门

Microsoft 提供一个框架来帮助你开始使用 Azure。 [云采用框架](/azure/architecture/cloud-adoption/) (CAF) 提供实现企业数字变革的详细措施，以及有关规划生产级云采用方案的综合指导。 CAF 包含循序渐进的 [Azure 设置指南](/azure/cloud-adoption-framework/ready/azure-setup-guide/)，以帮助你快速安全地开始运作。 可以在 [Azure 门户](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)中找到交互式版本。 在其中可以找到示例体系结构、有关部署应用程序的具体最佳做法，以及用于将你从新手培养成为 Azure 专家的免费培训资源。

### <a name="consider-the-network-between-your-location-and-azure"></a>考虑你所在位置与 Azure 之间的网络

不管是使用云资源来进行生产、测试和开发，还是将其用作备份目标和恢复站点，都必须了解初始备份种子设定和日常数据传输的带宽需求。

Azure Data Box 提供了一种无需更多带宽即可将初始备份基线传输到 Azure 的方式。 这在预计的基线传输所需时间超过了你的容忍范围时会很有用。 创建存储帐户时，可以使用数据传输估算器来估算传输初始备份所需的时间。

![显示门户中的 Azure 存储数据传输估算器。](../media/az-storage-transfer.png)

请记住，需有足够的网络容量才能在不影响生产应用程序的情况下，在所需的传输期限（备份期限）内支持每日数据传输。 本部分概述了可用于评估网络需求的工具和方法。

#### <a name="determine-how-much-bandwidth-youll-need"></a>确定需要多大的带宽

可以使用多个评估选项来确定传输到 Azure 的初始基线的更改率和备份集总大小。 下面是一些评估和报告工具示例：

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
| **所需的最短数据保留期** | NA | 30 天 | 180 天 |
| **延迟（距收到第一个字节的时间）** | 毫秒 | 毫秒 | 小时 |

#### <a name="sample-backup-to-azure-cost-model"></a>备份到 Azure 成本模型的示例

对于不熟悉云的客户而言，按用量付费可能令人望而生畏。 尽管你只需为使用的容量付费，但使用 [Azure Express Route Direct Local 或 Express Route 不限流量套餐](https://azure.microsoft.com/pricing/details/expressroute/)（包括从 Azure 流出的数据）时，确实还要支付事务（读取或写入）费，以及读回到本地环境的[数据的流出](https://azure.microsoft.com/pricing/details/bandwidth/)费。 可以使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)来执行“假设”(what-if) 分析。 可将分析建立在价目表定价或 [Azure 存储预留容量定价](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)的基础之上，这样可以节省高达 38% 的成本。 以下示例定价练习为备份到 Azure 的每月成本建模。 这只是一个示例。 由于此处未捕获到某些活动，你的定价可能有所不同。

|成本因素  |每月成本  |
|---------|---------|
|冷存储中的 100 TB 备份数据     |$1556.48         |
|每天写入 2 TB 新数据 x 30 天     |$42 事务成本          |
|每月估算总计     |$1598.48         |
|---------|---------|
|通过公共 Internet 一次性将 5 TB 还原到本地   | $527.26         |

> [!Note]
> 此估算成本是在 Azure 定价计算器中使用美国东部即用即付定价生成的，并且基于 Veeam 默认的 512 kb 块大小（用于 WAN 传输）。 此示例可能不适合你的要求。

## <a name="implementation-guidance"></a>实施指南

本部分提供有关如何将 Azure 存储添加到本地 Veeam 部署的简要指导。 有关详细指导和规划注意事项，我们建议查看以下有关 Veeam [容量层](https://helpcenter.veeam.com/docs/backup/vsphere/capacity_tier.html?ver=110)的指南。

1. 打开 Azure 门户，搜索“存储帐户”。 也可以单击默认服务图标。

      ![展示如何在 Azure 门户中添加存储帐户。](../media/azure-portal.png)

      ![展示在 Azure 门户的搜索框中键入存储的位置。](../media/locate-storage-account.png)

2. 选择“创建”以添加帐户。 选择或创建一个资源组，提供唯一的名称，选择区域，选择“标准”性能，始终将帐户类型保留为“存储 V2”，选择符合 SLA 的复制级别，以及备份软件将要应用的默认层。  使用 Azure 存储帐户可在单个帐户中使用热层、冷层和存档层，Veeam 策略允许使用多个层来有效管理数据的生命周期。

    ![展示门户中的存储帐户设置](../media/account-create-1.png)

3. 暂时保留默认网络和数据保护选项。 “不要”为存储 Veeam 容量层的存储帐户启用软删除。

 4. 接下来，对于备份到 Azure 的用例，我们建议使用“高级”屏幕中的默认设置。

    ![展示门户中的“高级”设置选项卡。](../media/account-create-3.png)

5. 为组织添加标记（如果使用标记功能），并创建帐户。

6. 现在，只需完成两个快速步骤，就能将帐户添加到 Veeam 环境。 在 Azure 门户中导航到你创建的帐户，然后在“Blob 服务”菜单下选择“容器”。  添加一个容器并选择有意义的名称。 然后，导航到“设置”下的“访问密钥”项，复制“存储帐户名称”和两个访问密钥中的一个。   在后续步骤中，需要用到容器名称、帐户名称和访问密钥。

    ![展示如何在门户中创建容器。](../media/container-b.png)

    ![展示门户中的访问密钥设置。](../media/access-key.png)

    > [!Note]
    > Veeam 备份和复制提供额外的选项用于连接到 Azure。 对于本文中的用例，建议的最佳做法是按照上述方法使用 Microsoft Azure Blob 存储作为备份目标。

7. （可选）可将更多安全层添加到部署。

     1. 配置基于角色的访问，以限制可对你的存储帐户进行更改的人员。 有关详细信息，请参阅[用于管理操作的内置角色](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)。

     1. 使用[存储防火墙设置](../../../../common/storage-network-security.md)来限制只能通过特定的网段访问帐户，以防止有人试图从企业网络外部进行访问。

        ![展示门户中的存储防火墙设置。](../media/storage-firewall.png)

     1. 对帐户设置[删除锁](../../../../../azure-resource-manager/management/lock-resources.md)，以防止意外删除存储帐户。

        ![资源锁](../media/resource-lock.png)

     1. 配置附加[安全性的最佳做法](../../../../../storage/blobs/security-recommendations.md)。

8. 在 Veaam 备份和复制管理控制台中，导航到“备份基础结构”-> 在概述窗格中单击右键，然后选择“添加备份存储库”打开配置向导。  在对话框中，选择“对象存储” -> “Microsoft Azure Blob 存储” -> “Azure Blob 存储”。  

    ![展示如何在 Veeam 存储库向导中选择“对象存储”。](../media/veeam-repo-a.png)

    ![展示如何在 Veeam 存储库向导中选择“Microsoft Azure Blob 存储”。](../media/veeam-repo-b.png)

    ![展示如何在 Veeam 存储库向导中选择“Azure Blob 存储”。](../media/veeam-repo-c.png)

9. 接下来，为新的 Blob 存储存储库指定名称和说明。

    ![展示如何在 Veeam 存储库向导中键入存储库的名称。](../media/veeam-repo-d.png)

10. 在下一步骤中，添加用于访问 Azure 存储帐户的凭据。 在云凭据管理器中选择“Microsoft Azure 存储帐户”，然后输入存储帐户名称和访问密钥。 在区域选择器中选择“Azure 全球”，并选择任一网关服务器（如果适用）。

    ![展示如何在 Veeam 存储库向导中指定帐户。](../media/veeam-repo-e.png)

    > [!Note]
    > 如果你不打算使用 Veeam 网关服务器，请确保所有扩展存储库区都可直接访问 Internet。

11. 在容器注册表中选择你的 Azure 存储容器，然后选择或创建一个用于存储备份的文件夹。 还可以对 Veeam 使用的存储总容量定义软限制（建议这样做）。 查看摘要部分中显示的信息并完成配置工具中的步骤。 现在可以在备份作业配置中选择新的存储库。

    ![展示如何在 Veeam 存储库向导中指定容器。](../media/veeam-repo-f.png)

    ![展示如何在 Veeam 存储库向导中创建文件夹。](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>操作指南

### <a name="azure-alerts-and-performance-monitoring"></a>Azure 警报和性能监视

建议监视 Azure 资源以及 Veeam 的资源利用能力，就像使用你所依赖的用于存储备份的存储目标时一样。 将 Azure Monitor 与 Veeam 的监视功能（Veeam 管理控制台的“作业”节点中的“统计信息”选项卡，或者 Veeam One Reporter 等更高级选项）相结合，可帮助你保持环境正常运行。 

#### <a name="azure-portal"></a>Azure 门户

Azure 以 [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md) 的形式提供可靠的监视解决方案。 可以[配置 Azure Monitor](../../../../blobs/monitor-blob-storage.md) 来跟踪 Azure 存储容量、事务、可用性、身份验证等。 可在[此处](../../../../blobs/monitor-blob-storage-reference.md)找到跟踪的指标的完整参考。 可跟踪的几个有用指标包括：BlobCapacity - 确保不会超过最大[存储帐户容量限制](../../../../common/scalability-targets-standard-account.md)；Ingress 和 Egress - 跟踪向/从 Azure 存储帐户写入/读取的数据量；SuccessE2ELatency - 跟踪向/从 Azure 存储和 MediaAgent 发出/接收请求的往返时间。

还可以[创建日志警报](../../../../../service-health/alerts-activity-log-service-notifications-portal.md)来跟踪 Azure 存储服务的运行状况，并随时查看 [Azure 状态仪表板](https://status.azure.com/status)。

#### <a name="veeam-reporting"></a>Veeam 报告

- [配置 Veeam One Reporting](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Veeam 备份和复制警报](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>如何提出支持案例

在备份到 Azure 解决方案过程中需要帮助时，应该向 Veeam 和 Azure 两方面提出案例。 这有助于我们的支持单位在必要时展开协作。

#### <a name="to-open-a-case-with-veeam"></a>若要向 Veeam 提出案例

在 [Veeam 客户支持网站](https://www.veeam.com/support.html)上登录，然后提出案例。

若要了解 Veeam 提供的支持选项，请参阅 [Veeam 客户支持策略](https://www.veeam.com/veeam_software_support_policy_ds.pdf)。

还可以通过电话提出案例：[全球支持电话号码](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>若要向 Azure 提出案例

在 [Azure 门户](https://portal.azure.com)的顶部搜索栏中搜索“支持”。 选择“帮助 + 支持” -> “新建支持请求” 。

> [!NOTE]
> 提交案例时，明确指出你需要 Azure 存储或 Azure 网络方面的帮助。 请勿指定 Azure 备份。 Azure 备份是 Azure 服务的名称，如果指定，你的案例将移交到错误的人员。

### <a name="links-to-relevant-veeam-documentation"></a>相关 Veeam 文档的链接

请参阅以下 Veeam 文档了解更多详细信息：

- [Veeam 用户指南](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Veeam 体系结构指南](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>市场产品/服务

你可以持续使用自己了解且信任的 Veeam 解决方案来保护 Azure 上运行的工作负载。 Veeam 可以方便客户在 Azure 中部署 Veeam 的解决方案以及保护 Azure 虚拟机和其他许多 Azure 服务。

- [通过 Azure 市场部署 Veeam 备份和复制](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Veeam 的 Azure 备份和恢复网站](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>后续步骤

参阅 Veeam 网站上的以下资源获取有关专业使用方案的信息：

- [Veeam 操作指南视频](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Veeam 技术文档](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Veeam 知识库和常见问题解答](https://www.veeam.com/knowledge-base.html?ad=menu-resources)