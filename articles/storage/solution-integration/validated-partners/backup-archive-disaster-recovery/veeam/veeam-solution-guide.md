---
title: 通过 Veeam 将数据备份到 Azure
titleSuffix: Azure Blob Storage Docs
description: 网页概述了要考虑的因素，以及使用 Azure 作为存储目标和恢复位置进行 Veeam 备份和恢复的步骤
keywords: Veeam，，备份到云，备份，备份到 Azure，灾难恢复，业务连续性
author: Karl Rautenstrauch
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: Storage
ms.subservice: Blob Storage
ms.openlocfilehash: 9f07703d23c3be6e842a54ba4bb0d46467ccb71d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744536"
---
# <a name="backup-to-azure-with-veeam"></a>通过 Veeam 备份到 Azure

本文提供了将 Veeam 基础结构与 Azure Blob 存储集成的指南。 它包括必备组件、Azure 存储原则、实现和操作指南。 本文仅在发生灾难时使用 Azure 作为场外备份目标和恢复站点进行寻址，这会阻止主站点内的正常操作。 Veeam 还提供了较低的 RTO 解决方案 Veeam 复制，这种方法可让备用 VM 在发生灾难时可以更快地启动和恢复，并可保护 Azure 生产环境中的资源。 Veeam 还专门用于备份 Azure 和 Office 365 资源。 这些功能超出了本文档的范围。 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>本地到 Azure 的参考体系结构和 In-Azure 部署

![Veeam 到 Azure 参考体系结构](../media/veeam-architecture.png)

现有的 Veeam 部署可通过将 Azure 存储帐户或多个帐户添加为云备份存储库，轻松与 Azure 集成。 Veeam 还允许从 Azure 中的本地恢复备份，为你提供 Azure 中的按需恢复站点。

## <a name="veeam-interoperability-matrix"></a>Veeam 互操作性矩阵
| 工作负荷 | GPv2 和 Blob 存储 | 冷层支持 | 存档层支持 | Data Box 系列支持 |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| 本地 Vm/数据 | v10a | v10a | NA | 10a |
| Azure VM | v10a | v10a | NA | 10a |
| Azure Blob | v10a | v10a | NA | 10a |
| Azure 文件 | v10a | v10a | NA | 10a | 

> [!Note]
Veeam 备份和复制仅支持 Azure Data Box REST API，因此 Azure Data Box Disk 不受支持。 Veeam v11 中应提供对 Azure Blob 存储的存档层的支持。

## <a name="before-you-begin"></a>准备阶段

一段前期规划将确保您加入多个令人满意的客户的排名，使用 Azure 作为场外备份目标和恢复站点。

### <a name="are-you-new-to-azure"></a>你是否是 Azure 的新手？

Microsoft 提供了一个框架，让你开始使用 Azure。 [云采用框架](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \( CAF \) 是一种详细的企业数字转换方法，并提供规划生产级云采用的综合性指南。 CAF 包含 azure 的新 azure [设置指南](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) ，可帮助你快速、安全地启动和运行，并可在 [Azure 门户](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)中找到交互式版本。 你将找到有关部署应用程序的示例体系结构和具体的最佳实践，并提供免费的培训资源，让你了解 Azure 专业知识的路径。

### <a name="consider-the-network-between-your-location-and-azure"></a>考虑你所在位置与 Azure 之间的网络

无论是利用云资源来运行生产、测试和开发，还是作为备份目标和恢复站点，都一定要了解最初备份种子的带宽需求以及日常传输。

Azure Data Box 提供了一种方法，用于将初始备份基线传输到 Azure，而不需要额外的带宽。 创建存储帐户时，可以利用数据传输估计器来估计传输初始备份所需的时间。

![Azure 存储数据传输估计器](../media/az-storage-transfer.png)

请记住，你将需要足够的网络容量来支持所需传输时段内的每日数据传输 (备份时段) ，而不影响生产应用程序。 本部分将概述可用于评估网络需求的工具和技术。

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>如何确定需要多少带宽？

可以使用多个评估选项来确定初始基线传输到 Azure 的更改速率和备份集总大小。 下面是一些评估和报告工具示例，例如：
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="how-will-i-know-how-much-headroom-i-have-with-my-current-internet-connection"></a>我如何知道当前 Internet 连接有多少空间？

了解日常可用的带宽量（或通常是未利用的），这一点很重要。 这样，你就可以正确地评估你是否能满足初始时间上传的目标、未使用 Azure Data Box 进行脱机种子设定的情况，并根据上面标识的更改率和备份时段完成日常备份。 下面是一些可用于识别 Azure 备份可免费使用的带宽空间的方法。

- 你是否是现有的 Azure ExpressRoute 客户？ 查看 Azure 门户中的 [线路使用情况](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts#circuits-metrics) 。
- 您可以与您的 ISP 联系。 它们应该包含与你分享现有每日和每月使用情况的报表。
- 可以通过监视路由器/交换机级别的网络流量来衡量利用率，其中包括：
  - [Solarwinds 带宽分析器包](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco 网络助手](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp 金牌](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choosing-the-right-storage-options"></a>选择正确的存储选项

使用 Azure 作为备份目标时，客户将使用 [Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)\. Azure Blob 存储是 Microsoft 的对象存储解决方案。 Blob 存储经过优化，可存储大量非结构化数据，这些数据不符合任何数据模型或定义。 此外，Azure 存储空间持久、高度可用、安全且可缩放。 Microsoft 的平台为为适当的工作负荷选择适当的存储提供了灵活性，以便为满足您的内部 Sla 提供 [弹性级别](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json) 。 Blob 存储是按使用付费服务。 系统会按月对存储的数据量进行收费，并访问该数据，如果是冷层和存档层，则需要 [按月收费](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal#pricing-and-billing) ，这是最低要求的保持期。 下表总结了适用于备份数据的复原和分层选项。

**Azure Blob 存储复原选项：**

|  |本地冗余  |区域冗余  |地域冗余  |地域冗余  |
|---------|---------|---------|---------|---------|
|有效的副本数     | 3         | 3         | 6         | 6 |
|可用性区域数     | 1         | 3         | 2         | 4 |
|区域数     | 1         | 1         | 2         | 2 |
|手动故障转移到次要区域     | NA         | NA         | 是         | 是 |

**Azure Blob 存储层：**

|  | 热层   |冷层   | 存档层级 |
| ----------- | ----------- | -----------  | -----------  |
| 可用性 | 99.9%         | 99%         | Offline      |
| 使用费 | 更高的存储成本、更低的访问和事务成本 | 存储费用较低，访问和事务费用较高 | 存储费用最低，访问和事务费用最高 |
| 需要最少的数据保留 | NA | 30 天 | 180 天 |
| 到第一个字节的延迟 (时间)  | 毫秒 | 毫秒 | 小时 |

#### <a name="sample-backup-to-azure-cost-model"></a>示例备份到 Azure 成本模型

对于不熟悉公有云的客户而言，使用按使用付费的概念可能会很困难。 当只为使用的容量付费时，还需支付事务 (读取和或写入) 和 [传出数据，以便](https://azure.microsoft.com/pricing/details/bandwidth/) 在 [Azure Express route 直接本地或快速路由无限制数据计划](https://azure.microsoft.com/pricing/details/expressroute/) 正在使用中，其中包括来自 azure 的数据出口。 你可以执行基于列表定价或 [Azure 存储预留容量定价](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)的假设分析，它可以在 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)中提供高达38% 的节省量。 下面是一个示例定价示例，用于模拟备份到 Azure 的每月成本，这只是一个示例， ***定价可能因此处未捕获的活动而异：***


|成本因素  |每月成本  |
|---------|---------|
|冷存储上的 100 TB 备份数据     |$1556.48         |
|每天 x 30 天写入的 2 TB 新数据     |事务中的 $72          |
|每月估计总计     |$1628.48         |
|---------|---------|
|一次性将 5 TB 还原到公共 Internet 上本地   | $527.26         |

> [!Note]
这项估计是使用美国东部即用即付定价在 Azure 定价计算器中生成的，并且基于 WAN 传输的 Veeam 默认值为256kb 块大小。 此示例可能不适用于你的要求。

## <a name="implementation-and-operational-guidance"></a>实现和操作指南

本部分提供了有关将 Azure 存储添加到本地 Veeam 部署的简短指南。 如果你对详细指南和规划注意事项感兴趣，我们建议查看 [Veeam Cloud Connect 备份指南](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100)。

1. 打开 Azure 门户，搜索 "存储帐户" 或单击 "默认服务" 图标。

      ![Azure 门户](../media/azure-portal.png)

      ![Azure 门户中的存储帐户](../media/locate-storage-account.png)

2. 选择 "添加帐户"，然后选择或创建资源组，提供唯一的名称，选择区域，选择 "标准" 性能，始终将帐户类型保留为 "Storage V2"，选择满足 Sla 的复制级别以及备份软件将利用的默认层。 Azure 存储帐户允许在单个帐户中使用热、冷和存档层，Veeam 策略允许你利用多个层来有效地管理数据的生命周期。 继续下一步。 
    
      ![创建存储帐户](../media/account-create-1.png)

3. 立即转到默认网络选项，然后转到 "数据保护"。 在此处，你可以选择启用 "软删除"，这样可以在定义的保留期内恢复意外删除的备份文件，并为意外或恶意删除提供保护。 
    ![创建存储帐户第2部分](../media/account-create-2.png)

4. 接下来，建议使用 "高级" 屏幕中的默认设置，以备份到 Azure 用例。

    ![创建存储帐户第3部分](../media/account-create-3.png) 

5. 如果利用标记并创建帐户，请为组织添加标记。 你现在可以使用大量的按需存储！

6. 在将帐户添加到 Veeam 环境之前，只需要两个快速步骤。 导航到在 Azure 门户中创建的帐户，并在门户边栏选项卡中的 "Blob 服务" 菜单下选择 "容器"。 添加新容器并选择有意义的名称。 然后，导航到 "设置" 下的 "访问密钥" 项，复制 "存储帐户名称" 和两个访问密钥中的一个。 在接下来的步骤中，将需要容器名称、帐户名和访问密钥。

    ![创建容器](../media/container-b.png)
    
    ![获取该帐户信息](../media/access-key.png)
    
    > [!Note]
Veeam 备份和复制提供连接到 Azure 的其他选项。 对于本文的使用案例，利用将 Microsoft Azure Blob 存储作为备份目标，使用上述方法是推荐的最佳做法。

7. ***(可选)*** 您可以向您的部署中添加其他安全层。

     1. 配置基于角色的访问权限，以限制可对你的存储帐户进行更改的人员。 [在此处了解详细信息](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)

    1. 使用 [存储防火墙](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) 限制对帐户的访问，以防止从公司网络外部进行访问。

    ![存储防火墙](../media/storage-firewall.png) 

    1. 设置帐户上的 [删除锁定](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) ，以防止意外删除存储帐户。

    ![资源锁 ](../media/resource-lock.png) 1. ) 配置其他 [安全最佳做法](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)。 
8. 在 Veaam 备份和复制管理控制台中，导航到 "备份基础结构"--> 在概述窗格中单击右键，然后选择 "添加备份存储库" 以打开配置向导。 在对话框中，选择 "对象存储--> Microsoft Azure Blob 存储"，> "Azure Blob 存储"。
    
    ![Veeam 存储库向导屏幕 a](../media/veeam-repo-a.png)

    ![Veeam 存储库向导屏幕 b](../media/veeam-repo-b.png)

    ![Veeam 存储库向导屏幕 c](../media/veeam-repo-c.png)

9. 接下来，指定新 Microsoft Azure Blob 存储库的名称和描述。
    
    ![Veeam 存储库向导屏幕 d](../media/veeam-repo-d.png)

10. 在下一步中，添加用于访问 Azure 存储帐户的凭据。 在云凭据管理器中选择 "Microsoft Azure 存储帐户"，输入你的存储帐户名称和访问密钥。 如果适用，请选择区域选择器和任何网关服务器中的 "Azure 全局"。
    
    ![Veeam 存储库向导屏幕 e](../media/veeam-repo-e.png)

> [!Note]
如果选择不使用 Veeam 网关服务器，请确保所有扩展存储库区都具有直接 internet 访问权限。

11. 在容器注册上，选择 Azure 存储容器，然后选择或创建用于存储备份的文件夹。 还可以对 Veeam (建议) 使用的总体存储容量定义软限制。 查看 "摘要" 部分中显示的信息并完成配置工具。 然后，可以在备份作业配置中选择新存储库。

    ![Veeam 存储库向导屏幕 f](../media/veeam-repo-f.png)
    
    ![Veeam 存储库向导屏幕 g](../media/veeam-repo-g.png)

### <a name="azure-alerting-and-performance-monitoring"></a>Azure 警报和性能监视

建议同时监视 Azure 资源和 Veeam 的功能，就像使用任何依赖于存储备份的存储目标一样。 Azure Monitor 和 Veeam 的监视功能的组合 (例如，Veeam 管理控制台的 "作业" 节点中的 "统计信息" 选项卡或更多高级选项，例如 Veeam 一个报告器) 将有助于保持环境正常运行。

#### <a name="microsoft-azure-portal"></a>Microsoft Azure 门户

Microsoft Azure 以 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)的形式提供可靠的监视解决方案。 可以 [配置 Azure Monitor](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) 来跟踪 Azure 存储容量、事务、可用性、身份验证等。 可在 [此处](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference)找到跟踪的指标的完整参考。 需要跟踪的几个有用指标是 Blobcapacity 相同，以确保保留在最大 [存储帐户容量限制](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account)、入口和出口的范围内，以跟踪写入和读取 azure 存储帐户的数据量，并使用 SuccessE2ELatency 跟踪对 azure 存储和 MediaAgent 的请求的往返时间。 

你还可以 [创建日志警报](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) 来跟踪 azure 存储服务运行状况，并随时查看 [Azure 状态仪表板](https://status.azure.com/status) 。

#### <a name="veeam-reporting"></a>Veeam 报告

[配置 Veeam 报表](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)

[Veeam 备份和复制警报](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100) 

### <a name="how-to-open-support-cases"></a>如何打开支持案例

当你需要有关备份到 Azure 解决方案的帮助时，我们建议同时使用 Veeam 和 Azure 来打开案例，以便我们的支持组织可以在必要时协同合作。

#### <a name="how-to-open-a-case-with-veeam"></a>如何使用 Veeam 打开事例

导航到 " [Veeam 客户支持门户](https://www.veeam.com/support.html)"，登录并打开案例。

如果需要了解 Veeam 提供的支持选项，请参阅 [Veeam 客户支持策略](https://www.veeam.com/veeam_software_support_policy_ds.pdf)

你还可以调用来打开一个案例：

[全球支持编号](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>如何使用 Azure 支持团队开启案例

在 [Azure 门户](https://portal.azure.com) 搜索门户顶部的搜索栏中的 "支持"，并选择 "+ 新建支持请求" 
> [!Note]
打开案例时，请特别注意 "Azure 存储" 或 "Azure 网络"，而 **不** 是 "azure 备份"。 Azure 备份是一种 Microsoft Azure 的本机服务，不会正确路由你的情况。

### <a name="links-to-relevant-veeam-documentation"></a>相关 Veeam 文档的链接

提供更多详细信息的 Veeam 文档：

[Veeam 用户指南](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)

[Veeam 体系结构指南](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="link-to-marketplace-offering"></a>链接到 Marketplace 产品

你还可以继续使用你认识和信任的 Veeam 解决方案来保护在 Azure 上运行的工作负荷。 Veeam 让你可以轻松地在 Azure 中部署其解决方案并保护 Azure 虚拟机和其他许多 Azure 服务。

[通过 Azure Marketplace 部署 Veeam B&R](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)

[Azure 数据表](https://www.veeam.com/backup-azure.html?ad=menu-products)


## <a name="next-steps"></a>后续步骤

浏览这些外部网站上的其他资源，获取有关专用使用方案的信息：

[Veeam how to 视频](https://www.veeam.com/how-to-videos.html?ad=menu-resources)

[Veeam 技术文档](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)

[Veeam 知识库和常见问题](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
