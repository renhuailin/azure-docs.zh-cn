---
title: 使用 Komprise 智能数据管理器分析文件数据并将其迁移到 Azure
titleSuffix: Azure Storage
description: 实施 Komprise 智能数据管理器的入门指南。 指南介绍如何分析文件基础结构，以及如何将数据迁移到 Azure 文件存储、Azure NetApp 文件、Azure Blob 存储或任何可用的 ISV NAS 解决方案
author: dukicn
ms.author: nikoduki
ms.date: 05/20/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 97ad7067b598ad4aa03f46cc7124dcc05d7f2b42
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115684"
---
# <a name="analyze-and-migrate-to-azure-with-komprise"></a>使用 Komprise 分析数据并将其迁移到 Azure

本文帮助你将 Komprise 智能数据管理器基础结构与 Azure 存储服务集成。 文中包括有关如何分析和迁移数据的注意事项和实现指南。

Komprise 提供对网络连接存储系统的分析和见解。 通过它可将数据迁移到 Azure 文件存储、Azure NetApp 文件、Azure Blob 存储或 ISV NAS 解决方案等 Azure 存储服务。 详细了解[经验证的主存储和辅助存储合作伙伴解决方案](../primary-secondary-storage/partner-overview.md)。

Komprise 将在许多不同的用例中中提供帮助，例如：

- 分析本地非结构化数据，获取对数据管理、移动、定位、存档、保护和限制的见解。
- 将本地非结构化数据迁移到 Azure 文件存储、Azure NetApp 文件或 ISV NAS 解决方案
- 使用本机访问将本地非结构化数据复制或存档到 Azure Blob 存储
- 将对象存储解决方案迁移到 Azure Blob 存储

## <a name="reference-architecture"></a>参考体系结构

下图提供了本地到 Azure 的部署以及 Azure 内部部署的参考体系结构。

:::image type="content" source="./media/komprise-quick-start-guide/komprise-architecture.png" alt-text="参考体系结构介绍了 Komprise 智能数据管理器的基本设置":::

Komprise 是一种软件解决方案，可轻松部署在虚拟环境中。 解决方案包括：
- 控制器 - Komprise 网格的管理控制台。 它用于配置环境、监视活动、查看报表和图形，以及设置策略。
- 观察器 - 管理和分析共享、汇总报表、与控制器通信，以及处理 NFS 数据流量。
- 代理 - 简化和加速 SMB/CIFS 数据流；易于扩展，可满足不断发展的环境的性能要求。

## <a name="before-you-begin"></a>在开始之前

提前规划将有助于降低迁移数据的风险。

### <a name="get-started-with-azure"></a>Azure 入门

Microsoft 提供一个框架来帮助你开始使用 Azure。 [云采用框架](/azure/architecture/cloud-adoption/) (CAF) 提供实现企业数字变革的详细措施，以及有关规划生产级云采用方案的综合指导。 CAF 包含循序渐进的 [Azure 设置指南](/azure/cloud-adoption-framework/ready/azure-setup-guide/)，帮助你快速安全地开始运作。 可以在 [Azure 门户](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)中找到交互式版本。 在其中可以找到示例体系结构、有关部署应用程序的具体最佳做法，以及用于将你从新手培养成为 Azure 专家的免费培训资源。

### <a name="considerations-for-migrations"></a>迁移注意事项

考虑将文件数据迁移到 Azure 时，需要注意几个方面。 继续之前，请先详细了解以下内容：

- [Azure 存储迁移概述](../../../common/storage-migration-overview.md)
- Komprise 智能数据管理在[迁移工具比较矩阵](./migration-tools-comparison.md)中支持的最新功能。

请记住，需有足够的网络容量才能在不影响生产应用程序的情况下支持迁移。 本部分概述了可用于评估网络需求的工具和方法。

#### <a name="determine-unutilized-internet-bandwidth"></a>确定未利用的 Internet 带宽

必须知道在日常中通常有多少带宽不被利用到（即“空余空间”）。 帮助评估是否达到以下目标：

- 不将 Azure Data Box 用于脱机方法时的初始迁移时间
- 在最终切换到目标文件服务之前执行增量重新同步所需的时间

使用以下方法来确定到 Azure 的可任意占用的带宽空余空间。

- 如果你是现有的 Azure ExpressRoute 客户，请在 Azure 门户中查看[线路用量](../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics)。
- 请与 ISP 联系并请求提供报表，以显示当前每日和每月利用率。
- 有多个工具可以通过监视路由器/交换机级别的网络流量来测量利用率：
  - [SolarWinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

## <a name="migration-planning-guide"></a>迁移规划指南

Komprise 易于设置，且只需三步即可实现多个迁移的同时运行：

1.  分析数据以确定要迁移或存档的文件；
1.  定义策略，用于将非结构化数据迁移、移动或复制到 Azure 存储；
1.  激活自动移动数据的策略。

第一步对于查找要迁移的正确数据并确定其优先级非常重要。 Komprise 分析提供：

- 访问时间的信息，用于标识：
  - 可在本地缓存或存储到快速文件服务上的不经常访问的文件
  - 可存档到 Blob 存储的冷数据
- 关于主要用户、组或共享的信息，用于确定迁移顺序和对组织内受影响最大的组以评估业务影响
- 文件数量或每个文件类型的容量，用于确定存储文件的类型，以及是否有任何清理内容的可能性。 清理会减少迁移工作量，并降低目标存储的成本
- 文件数量或每个文件大小的容量，用于确定迁移时长。 与少量的大型文件相比，大量小型文件的迁移需更长时间

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-1.png" alt-text="按文件类型和访问时间分析":::

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-shares.png" alt-text="共享分析的示例":::

- 自定义查询功能筛选器，用于针对特定需求筛选精确文件集

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-custom.png" alt-text="自定义查询分析":::

## <a name="deployment-guide"></a>部署指南

在部署 Komprise 前，必须部署目标服务。 可在此处了解详细信息：

- 如何创建 [Azure 文件共享](../../../files/storage-how-to-create-file-share.md)
- 在 Azure NetApp 文件中，如何创建 [SMB 卷](../../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)或 [NFS 导出](../../../../azure-netapp-files/azure-netapp-files-create-volumes.md)

Komprise 网格部署在虚拟环境中（Hyper-V、VMware、KVM），以提高速度、可伸缩性和适应性。 或者，可在 Azure 订阅中使用 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management)设置环境。

1. 打开 Azure 门户，并搜索“存储帐户”。 

    :::image type="content" source="./media/komprise-quick-start-guide/azure-locate-storage-account.png" alt-text="展示在 Azure 门户的搜索框中键入存储的位置。":::

    还可以单击默认的“存储帐户”图标。

    :::image type="content" source="./media/komprise-quick-start-guide/azure-portal.png" alt-text="显示如何在 Azure 门户中添加存储帐户。":::

2. 选择“创建”以添加帐户：
   1. 选择现有资源组或“新建”
   2. 为存储帐户提供唯一的名称
   3. 选择区域
   4. 根据需求选择“标准”或“高级”性能 。 如果选择“高级”，请在“高级帐户类型”中选择“文件共享”  。
   5. 选择满足数据保护要求的[冗余](../../../common/storage-redundancy.md)
   
   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-1.png" alt-text="显示门户中的存储帐户设置。":::

3. 接下来，推荐使用“高级”屏幕中的默认设置。 如果要迁移到 Azure 文件存储，建议启用“大型文件共享”（如果可用）。

   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-2.png" alt-text="展示门户中的“高级”设置选项卡。":::

4. 暂时保留默认网络选项并转到“数据保护”。 可以选择启用软删除，这样就可以在定义的保持期内恢复意外删除的数据。 软删除提供针对意外删除或恶意删除的保护。

   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-3.png" alt-text="展示门户中的“数据保护”设置。":::

5. 为组织添加标记（如果使用标记功能），并“创建”帐户。
 
6. 现在只需完成两个简要步骤，就可将帐户添加到 Komprise 环境。 在 Azure 门户中导航到创建的帐户，然后在“文件服务”菜单下选择“文件共享”。 添加一个文件共享并选择有意义的名称。 然后，导航到“设置”下的“访问密钥”项，复制“存储帐户名称”和两个访问密钥中的一个。   如果未显示密钥，请单击“显示密钥”。

   :::image type="content" source="./media/komprise-quick-start-guide/azure-access-key.png" alt-text="展示门户中的访问密钥设置。":::

7. 导航到 Azure 文件共享的“属性”。 记下 URL 地址，在将 Azure 连接添加到 Komprise 目标文件共享时需要它：

   :::image type="content" source="./media/komprise-quick-start-guide/azure-files-endpoint.png" alt-text="查找 Azure 文件存储终结点。":::

8. （可选）可将更多安全层添加到部署。
 
   1. 配置基于角色的访问，以限制可对你的存储帐户进行更改的人员。 有关详细信息，请参阅[用于管理操作的内置角色](../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)。
 
   2.  通过[存储防火墙设置](../../../common/storage-network-security.md)将对帐户的访问限制为只能通过特定网段访问。 配置防火墙设置以阻止来自企业网络外部的访问。

       :::image type="content" source="./media/komprise-quick-start-guide/azure-storage-firewall.png" alt-text="展示门户中的存储防火墙设置。":::

   3.  对帐户设置[删除锁](../../../../azure-resource-manager/management/lock-resources.md)，以防止意外删除存储帐户。

       :::image type="content" source="./media/komprise-quick-start-guide/azure-resource-lock.png" alt-text="显示如何在门户中设置删除锁。":::

   4.  配置其他[安全性最佳做法](../../../blobs/security-recommendations.md)。

1.  从控制器下载 Komprise 观察器虚拟设备，将其部署到虚拟机监控程序，并为其配置网络和域。 控制器以一项由 Komprise 管理的云服务的形式提供。 购买解决方案后，访问控制器所需的信息将随“欢迎使用”电子邮件一起发送。

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-1.png" alt-text="从控制器下载 Komprise 观察器的相应图片":::

1. 若要添加共享以进行分析和迁移，可使用以下两个选项：
   1. 输入以下内容，发现存储环境中的所有共享：
       - 用于源 NAS 的平台
       - 主机名或 IP 地址
       - 显示名称
       - 凭据（用于 SMB 共享）

        :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-2.png" alt-text="指定要发现的 NAS 系统":::

    1. 输入以下内容，指定一个文件共享：
       - 存储信息
       - 协议
       - 路径
       - 显示名称
       - 凭据（用于 SMB 共享）
  
        :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-3.png" alt-text="指定要发现的 NAS 解决方案":::

    若要添加其他源共享和目标共享，必须重复此步骤。 若要将 Azure 文件存储添加为目标，需提供 Azure 存储帐户和文件共享详细信息：

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-azure-files-1.png" alt-text="选择 Azure 文件存储作为目标服务":::

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-azure-files-2.png" alt-text="输入 Azure 文件存储的详细信息":::

## <a name="migration-guide"></a>迁移指南

Komprise 提供实时迁移，在迁移过程中，最终用户和应用程序不会受干扰，可持续访问数据。 迁移过程会自动将目录、文件和链接从源迁移到目标。 数据完整性在每个步骤都会进行检查。 源的所有属性、权限和访问控制都会应用。

若要配置和运行迁移，请执行以下步骤：

1. 登录到 Komprise 控制台。 购买解决方案后，访问控制台所需的信息将随“欢迎使用”电子邮件一起发送。
1. 导航到“迁移”，单击“添加迁移” 。

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-new-migrate.png" alt-text="添加新的迁移作业":::

1. 选择适当的源共享和目标共享，以添加迁移任务。 提供迁移名称。 配置后，单击“开始迁移”。 
   
   :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-migration.png" alt-text="指定迁移作业的详细信息":::

   （可选）定义是否需要保留目标上的访问时间和 SMB ACL。 此选项取决于所选的源和目标文件服务及协议。

2. 开始迁移后，可转到“迁移”来监视进度。

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-monitor-migrations.png" alt-text="监视所有迁移作业":::

3. 迁移所有更改后，单击“操作”并选择“启动最终迭代”，运行最后一次迁移 。 在进行最终迁移前，建议停止对源文件共享的访问，或将其移动到只读模式（针对用户和应用程序）。 此步骤将确保源不会发生任何更改。

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-final-migration.png" alt-text="在切换之前执行最后一次迁移":::

    完成最终迁移后，将所有用户和应用程序转换为目标共享。 切换到新的文件服务通常需要更改 DNS 服务器、DFS 服务器的配置，或更改新目标的装入点。 

4. 最后一步，将迁移标记为已完成。

## <a name="support"></a>支持

若要向 Komprise 提出案例，请登录到 [Komprise 支持网站](https://komprise.freshdesk.com/)

## <a name="marketplace"></a>市场

在 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)上获取 Komprise 清单。

## <a name="next-steps"></a>后续步骤

提供以下各种资源供了解详细信息：

- [Azure 存储迁移概述](../../../common/storage-migration-overview.md)
- Komprise 智能数据管理在[迁移工具比较矩阵](./migration-tools-comparison.md)中支持的功能
- [Komprise 兼容性矩阵](https://www.komprise.com/partners/microsoft-azure/)
