---
title: 使用专用终结点安全访问 Purview
description: 本文简要概述如何为 Purview 帐户使用专用终点
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: b65820ab72c0a20953a7e3e9e4a2ee964df51e07
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831609"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>为 Azure Purview 帐户使用专用终结点

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
|方案 1 - [以私密且安全的方式连接到你的 Purview 帐户](./catalog-private-link-account-portal.md)   | 需要启用对 Azure Purview 帐户的访问，包括通过专用终结点对 Azure Purview Studio 和 Atlas API 的访问。 （部署帐户和门户专用终结点） 。   |
|方案 2 - [以私密且安全的方式扫描数据源](./catalog-private-link-ingestion.md)  | 需要使用自承载集成运行时扫描本地和 Azure 中虚拟网络后面的数据源。 （部署引入专用终结点。）    |
|方案 3 - [以私密且安全的方式连接到你的 Azure Purview 并扫描数据源](./catalog-private-link-end-to-end.md) |需要限制为只能通过专用终结点对你的 Azure Purview 帐户进行访问（包括对 Azure Purview Studio 和 Atlas API 的访问），并使用自承载集成运行时扫描本地和 Azure 中虚拟网络后面的数据源，以确保端到端的网络隔离。 （部署帐户、门户和引入专用终结点。）     |

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

## <a name="frequently-asked-questions"></a>常见问题  

有关 Azure Purview 中的专用终结点部署的常见问题解答，请参阅[有关 Azure Purview 专用终结点的常见问题解答](./catalog-private-link-faqs.md)。
 
## <a name="troubleshooting-guide"></a>故障排除指南 
有关如何排查 Purview 帐户的专用终结点配置问题，请参阅[排查 Purview 帐户的专用终结点配置问题](./catalog-private-link-troubleshoot.md)。

## <a name="known-limitations"></a>已知限制
若要查看 Azure Purview 专用终结点相关的当前限制列表，请参阅 [Azure Purview 专用终结点已知限制](./catalog-private-link-troubleshoot.md#known-limitations)。

## <a name="next-steps"></a>后续步骤

- [部署引入专用终结点](./catalog-private-link-ingestion.md)
