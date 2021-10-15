---
title: 使用专用终结点安全访问 Purview
description: 本文简要概述如何为 Purview 帐户使用专用终点
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 9d92a1baddbd12f80084dbbdb9a9205edb3f56b1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272844"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>为 Azure Purview 帐户使用专用终结点

> [!IMPORTANT]
> 如果在 2021 年 9 月 27 日 15:30 (UTC) 之前创建了 Purview 帐户的门户专用终结点，则需按[为门户专用终结点重新配置 DNS](#reconfigure-dns-for-portal-private-endpoints) 中的详细说明执行所需操作。 必须在 2021 年 10 月 11 日之前完成这些操作，否则会导致现有门户专用终结点停止运行。


本文介绍如何为 Azure Purview 配置专用终结点。

## <a name="conceptual-overview"></a>概念概述
可为 Azure Purview 帐户使用 [Azure 专用终结点](../private-link/private-endpoint-overview.md)，使虚拟网络 (VNet) 上的用户能够通过专用链接安全访问目录。 专用终结点为 Purview 帐户使用 VNet 地址空间中的 IP 地址。 VNet 上的客户端与 Purview 帐户之间的网络流量通过 VNet 和 Microsoft 主干网络上的专用链接进行传输。 

可以部署 Azure Purview 帐户专用终结点，以便仅允许从专用网络内部发起的对 Azure Purview 的客户端调用。

若要使用专用网络连接来与 Azure Purview Studio 建立连接，可以部署门户专用终结点。

如果需要通过专用连接扫描 Azure 虚拟网络内部的 Azure IaaS 和 PaaS 数据源以及本地数据源，可以部署引入专用终结点。 此方法可确保从数据源流向 Azure Purview 数据映射的元数据保持网络隔离。

:::image type="content" source="media/catalog-private-link/purview-private-link-overview.png" alt-text="显示具有专用终结点的 Azure Purview 的屏幕截图。"::: 

## <a name="prerequisites"></a>先决条件

在为 Azure Purview 帐户部署专用终结点之前，请确保满足以下先决条件：

1. 具有活动订阅的 Azure 帐户。 [免费创建帐户。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
<br>
2. 一个现有的 Azure 虚拟网络。 部署一个新的 [Azure 虚拟网络](../virtual-network/quick-create-portal.md)（如果没有）。
<br>

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Azure Purview 专用终结点部署方案

使用以下建议的清单部署具有专用终结点的 Azure Purview 帐户：

|场景  |目标  |
|---------|---------|
|方案 1 - [以私密且安全的方式连接到你的 Azure Purview 并扫描数据源](./catalog-private-link-end-to-end.md) |需要限制为只能通过专用终结点对你的 Azure Purview 帐户进行访问（包括对 Azure Purview Studio 和 Atlas API 的访问），并使用自承载集成运行时扫描本地和 Azure 中虚拟网络后面的数据源，以确保端到端的网络隔离。 （部署帐户、门户和引入专用终结点。）     |
|方案 2 - [以私密且安全的方式连接到你的 Purview 帐户](./catalog-private-link-account-portal.md)   | 需要启用对 Azure Purview 帐户的访问，包括通过专用终结点对 Azure Purview Studio 和 Atlas API 的访问。 （部署帐户和门户专用终结点） 。   |

## <a name="support-matrix-for-scanning-data-sources-through-_ingestion_-private-endpoint"></a>通过引入专用终结点扫描数据源的支持矩阵

在 Azure Purview 帐户中使用引入专用终结点并已禁用对数据源的公共访问的情况下，Azure Purview 可以扫描专用终结点后面的以下数据源：

|专用终结点后面的数据源  |集成运行时类型  |凭据类型  |
|---------|---------|---------|
|Azure Blob 存储 | 自承载 IR | Service Principal|
|Azure Blob 存储 | 自承载 IR | 帐户密钥|
|Azure Data Lake Storage Gen 2 | 自承载 IR| Service Principal|
|Azure Data Lake Storage Gen 2 | 自承载 IR| 帐户密钥|
|Azure SQL Database | 自承载 IR| SQL 身份验证|
|Azure SQL Database | 自承载 IR| Service Principal|
|Azure SQL 托管实例 | 自承载 IR| SQL 身份验证|
|Azure Cosmos DB| 自承载 IR| 帐户密钥|
|SQL Server | 自承载 IR| SQL 身份验证|
|Azure Synapse Analytics | 自承载 IR| Service Principal|
|Azure Synapse Analytics | 自承载 IR| SQL 身份验证|

## <a name="reconfigure-dns-for-portal-private-endpoints"></a>为门户专用终结点重新配置 DNS

如果在 2021 年 9 月 27 日 15:30 (UTC) 之前创建了 Purview 帐户的门户专用终结点，请按此部分的详细说明执行所需操作。

- 如果你使用的是自定义 DNS，或已将所需的 DNS A 记录直接添加到计算机的主机文件，则无需执行任何操作 。 

- 如果已为 Purview 帐户配置 Azure 专用 DNS 区域集成，请按以下步骤重新部署专用终结点以重新配置 DNS 设置： 

    1. 部署新的门户专用终结点：
       
        1. 转到 [Azure 门户](https://portal.azure.com)，然后单击进入 Azure Purview 帐户，在“设置”下选择“网络”，然后选择“专用终结点连接”  。

            :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal.png" alt-text="显示创建门户专用终结点的屏幕截图。":::

        2. 选择“+ 专用终结点”创建新的专用终结点。

        3. 填写基本信息。

        4. 在“资源”选项卡中，对于“资源类型”，选择“Microsoft.Purview/account”  。

        5. 对于“资源”，请选择“Azure Purview 帐户”；对于“目标子资源”，请选择“门户”。

        6. 在“配置”选项卡上，选择虚拟网络，然后选择 Azure 专用 DNS 区域，以创建新的 Azure DNS 区域。
            
            :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal-dns.png" alt-text="屏幕截图，显示如何创建门户专用终结点和 DNS 设置。":::

        7. 转到“摘要”页面，选择“创建”以创建门户专用终结点。

    2. 删除与 Purview 帐户关联的前一门户专用终结点。 

    3. 确保在部署门户专用终结点的过程中创建新的 Azure 专用 DNS 区域 (privatelink.purviewstudio.azure.com)，并且专用 DNS 区域中存在相应的 A 记录 (web)。 
    
    4. 确保可以成功加载 Azure Purview Studio。 新的 DNS 路由在重新配置 DNS 之后生效可能需要几分钟的时间（大约 10 分钟）。 如果不立即加载，可以等待几分钟，然后重试。
    
    5. 如果导航失败，请执行 nslookup web.purview.azure.com，它应该解析为关联到门户专用终结点的专用 IP 地址。
  
    6. 对所有现有的门户专用终结点重复上面的步骤 1 至 3。 

- 如果你已配置本地的或自定义的 DNS 解析（例如，你使用自己的 DNS 服务器，或者配置了主机文件），请执行以下操作： 

  - 如果 DNS 记录是 `web.purview.azure.com`，则无需执行任何操作。  
  - 如果 DNS 记录是 `web.privatelink.purview.azure.com`，请将记录更新为 `web.privatelink.purviewstudio.azure.com`。 

## <a name="frequently-asked-questions"></a>常见问题  

有关 Azure Purview 中的专用终结点部署的常见问题解答，请参阅[有关 Azure Purview 专用终结点的常见问题解答](./catalog-private-link-faqs.md)。
 
## <a name="troubleshooting-guide"></a>故障排除指南 
有关如何排查 Purview 帐户的专用终结点配置问题，请参阅[排查 Purview 帐户的专用终结点配置问题](./catalog-private-link-troubleshoot.md)。

## <a name="known-limitations"></a>已知限制
若要查看 Azure Purview 专用终结点相关的当前限制列表，请参阅 [Azure Purview 专用终结点已知限制](./catalog-private-link-troubleshoot.md#known-limitations)。

## <a name="next-steps"></a>后续步骤

- [部署端到端专用网络](./catalog-private-link-end-to-end.md)
- [为 Purview Studio 部署专用网络](./catalog-private-link-account-portal.md)
