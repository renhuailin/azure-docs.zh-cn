---
title: 通过 Datadobi DobiMigrate 将文件数据迁移到 Azure
titleSuffix: Azure Storage
description: 提供实现 Datadobi DobiMigrate，以及将数据迁移到 Azure 文件存储、Azure NetApp 文件或 ISV NAS 解决方案的入门指南
author: dukicn
ms.author: nikoduki
ms.date: 04/27/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 6984230044dc70e6e5e05c7ae0dcb4789065c436
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955355"
---
# <a name="migrate-data-to-azure-with-datadobi-dobimigrate"></a>通过 Datadobi DobiMigrate 将数据迁移到 Azure

本文可帮助将 Datadobi DobiMigrate 基础结构与 Azure 存储集成。 内容包括先决条件、注意事项、实施指导和操作指导。

使用 DobiMigrate，可在存储平台之间迁移文件和对象。 DobiMigrate 可以快速、轻松且经济高效地将数据从本地迁移到 Azure。

## <a name="reference-architecture"></a>参考体系结构

下图提供了本地到 Azure 的部署以及 Azure 内部部署的参考体系结构。

:::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-reference-architecture.png" alt-text="参考体系结构介绍了 DobiMigrate 的基本设置":::

现有的 DobiMigrate 部署可通过添加并配置 Azure 连接轻松与 Azure 集成。

## <a name="before-you-begin"></a>开始之前

稍微提前规划一下就有助于将 Azure 用作场外备份目标和恢复站点。

### <a name="get-started-with-azure"></a>Azure 入门

Microsoft 提供一个框架来帮助你开始使用 Azure。 [云采用框架](/azure/architecture/cloud-adoption/) (CAF) 提供实现企业数字变革的详细措施，以及有关规划生产级云采用方案的综合指导。 CAF 包含循序渐进的 [Azure 设置指南](/azure/cloud-adoption-framework/ready/azure-setup-guide/)，帮助你快速安全地开始运作。 可以在 [Azure 门户](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)中找到交互式版本。 在其中可以找到示例体系结构、有关部署应用程序的具体最佳做法，以及用于将你从新手培养成为 Azure 专家的免费培训资源。

### <a name="considerations-for-migrations"></a>迁移注意事项

考虑将文件数据迁移到 Azure 时，需要注意几个方面。 继续之前，请先详细了解以下内容：

- [存储迁移概述](../../../common/storage-migration-overview.md)
- [迁移工具比较矩阵](./migration-tools-comparison.md)中 DobiMigrate 支持的最新功能。

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

## <a name="implementation-guidance"></a>实施指南

本部分提供有关如何将 Azure 文件存储共享添加到本地 Azure DobiMigrate 部署的简要指南。 

1. 打开 Azure 门户，并搜索“存储帐户”。 

    :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-locate-storage-account.png" alt-text="展示在 Azure 门户的搜索框中键入存储的位置。":::

    还可以单击默认的“存储帐户”图标。

    :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-portal.png" alt-text="显示如何在 Azure 门户中添加存储帐户。":::

2. 选择“创建”以添加帐户：
   1. 选择现有资源组或“新建”
   2. 为存储帐户提供唯一的名称
   3. 选择区域
   4. 根据需求选择“标准”或“高级”性能 。 如果选择“高级”，请在“高级帐户类型”中选择“文件共享”  。
   5. 选择满足数据保护要求的[冗余](../../../common/storage-redundancy.md)
   
   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-1.png" alt-text="显示门户中的存储帐户设置。":::

3. 接下来，推荐使用“高级”屏幕中的默认设置。 如果要迁移到 Azure 文件存储，建议启用“大型文件共享”（如果可用）。

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-2.png" alt-text="展示门户中的“高级”设置选项卡。":::

4. 暂时保留默认网络选项并转到“数据保护”。 可以选择启用软删除，这样就可以在定义的保持期内恢复意外删除的数据。 软删除提供针对意外删除或恶意删除的保护。

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-account-create-3.png" alt-text="展示门户中的“数据保护”设置。":::

5. 为组织添加标记（如果使用标记功能），并“创建”帐户。
 
6. 现在只需完成两个简要步骤，就可以将帐户添加到 DobiMigrate 环境。 在 Azure 门户中导航到创建的帐户，然后在“文件服务”菜单下选择“文件共享”。 添加一个文件共享并选择有意义的名称。 然后，导航到“设置”下的“访问密钥”项，复制“存储帐户名称”和两个访问密钥中的一个。  

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-access-key.png" alt-text="展示门户中的访问密钥设置。":::

7. 导航到 Azure 文件存储共享的属性并记下 URL 地址，将 Azure 连接添加到 DobiMigrate 时需要该 URL：

   :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-files-endpoint.png" alt-text="查找 Azure 文件存储终结点。":::

8. （可选）可将更多安全层添加到部署。
 
   1. 配置基于角色的访问，以限制可对你的存储帐户进行更改的人员。 有关详细信息，请参阅[用于管理操作的内置角色](../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)。
 
   2.  通过[存储防火墙设置](../../../common/storage-network-security.md)将对帐户的访问限制为只能通过特定网段访问。 配置防火墙设置以阻止来自企业网络外部的访问。

       :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-storage-firewall.png" alt-text="展示门户中的存储防火墙设置。":::

   3.  对帐户设置[删除锁](../../../../azure-resource-manager/management/lock-resources.md)，以防止意外删除存储帐户。

       :::image type="content" source="./media/dobimigrate-quick-start-guide/azure-resource-lock.png" alt-text="显示如何在门户中设置删除锁。":::

   4.  配置其他[安全性最佳做法](../../../blobs/security-recommendations.md)。

9.  在 DobiMigrate 中，导航到“配置”>“文件服务器”。 单击“添加”，添加 Microsoft Azure 文件存储作为文件服务器类型：

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-server-type.png" alt-text="添加 Microsoft Azure 文件存储作为服务器类型。":::

10. 指定名称、Azure 文件存储连接详细信息和存储帐户凭据：
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-connection-details.png" alt-text="配置 Azure 文件存储连接详细信息。":::

11. 将代理分配到 Azure 文件存储连接，然后单击“测试连接”，以确认代理能够与 Azure 文件存储通信：
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-test-connection.png" alt-text="测试连接详细信息。":::

    显示连接测试结果：

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-test-results.png" alt-text="显示测试连接的结果。":::

12. 在“SMB 迁移共享”下，可看到在此存储帐户下已预配的所有 Azure 文件存储共享。 对于迁移范围内的共享，请将“映射”设置为手动，例如 ：
 
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobiprotect-azure-files-shares.png" alt-text="显示可用共享。":::

13. 单击“完成”以完成 Azure 文件存储配置。 然后，可以启动新的迁移任务。

### <a name="start-a-new-migration"></a>启动新迁移

DobiMigrate 可以通过手动添加迁移路径或使用批量导入来设置新的迁移。 批量导入会添加多个具有常见迁移选项的迁移。

若要启动新的迁移，请执行以下操作：

1. 单击仪表板上的“新建迁移”按钮。
   
    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-new-migration.png" alt-text="启动新迁移作业。":::

1. 选择要迁移的源和路径。

    :::image type="content" source="./media/dobimigrate-quick-start-guide/dobimigrate-select-source.png" alt-text="选择要迁移的源和路径。":::

1. 选择“目标”。
2. 验证协议并确认迁移选项。
3. 单击“完成”以完成迁移过程。

## <a name="support"></a>支持 

在迁移到 Azure 解决方案过程中需要帮助时，应该向 Datadobi 和 Azure 提出案例。

### <a name="to-open-a-case-with-datadobi"></a>向 Datadobi 提出案例

在 [Datadobi 支持站点](https://support.datadobi.com/s/)上，登录并提出案例。

### <a name="to-open-a-case-with-azure"></a>向 Azure 提出案例

在 [Azure 门户](https://portal.azure.com/)的顶部搜索栏中搜索“支持”。 选择“帮助 + 支持” -> “新建支持请求” 。

## <a name="marketplace"></a>市场

Datadobi 可以方便客户在 Azure 中部署解决方案以保护 Azure 虚拟机和其他许多 Azure 服务。 有关详细信息，请参阅以下资源：

- [通过 DobiMigrate 将文件数据迁移到 Azure](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=overview)

## <a name="next-steps"></a>后续步骤

有关详细信息，请访问我们的指南：

- [Azure 存储迁移概述](../../../common/storage-migration-overview.md)
- [DobiMigrate 用户手册](https://downloads.datadobi.com/NAS/olh/latest/dobimigrate.html)
- [DobiMigrate 先决条件指南](https://downloads.datadobi.com/NAS/guides/latest/prerequisites.html)
- [DobiMigrate 安装指南](https://downloads.datadobi.com/NAS/guides/latest/installguide.html)