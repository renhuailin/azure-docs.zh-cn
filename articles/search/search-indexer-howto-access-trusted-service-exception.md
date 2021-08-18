---
title: 使用受信任的服务异常对 Azure 存储进行索引器访问
titleSuffix: Azure Cognitive Search
description: 允许 Azure 认知搜索中的索引器访问安全地存储在 Azure 存储中的数据。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2021
ms.openlocfilehash: 73512b0c1729fbdbbc62e0063670de8405ddbcb5
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113000277"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>使用受信任的服务异常对 Azure 存储进行索引器访问（Azure 认知搜索）

Azure 认知搜索服务中用于访问 Azure 存储帐户中的数据的索引器可以利用[受信任的服务异常](../storage/common/storage-network-security.md#exceptions)功能来安全地访问数据。 此机制为无法授予[使用 IP 防火墙规则进行索引器访问](search-indexer-howto-access-ip-restricted.md)的客户提供了一种简单、安全且免费的替代方法来访问存储帐户中的数据。

> [!NOTE]
> 对通过受信任的服务异常访问存储帐户中的数据的支持仅限于 Azure Blob 存储和 Azure Data Lake Gen2 存储。 不支持 Azure 表存储。

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>步骤 1：使用托管标识配置连接

按照[使用托管标识设置到 Azure 存储帐户的连接](search-howto-managed-identities-storage.md)中的说明进行操作。 完成后，你将搜索服务作为受信任的服务向 Azure Active Directory 注册，并且你将在 Azure 存储中具有授予的权限，以便为搜索标识提供访问数据或信息的特定权限。

> [!NOTE]
> 这些说明为你提供将认知搜索配置为受信任服务的门户方法的指导。 若要在代码中完成此操作，可以使用 [REST API](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)、[Azure PowerShell](search-manage-powershell.md#create-a-service-with-a-system-assigned-managed-identity) 或 [Azure CLI](search-manage-azure-cli.md#create-a-service-with-a-system-assigned-managed-identity)。

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>步骤 2：允许受信任的 Microsoft 服务访问存储帐户

在 Azure 门户中，导航到存储帐户的“防火墙和虚拟网络”选项卡。 确保已选中“允许受信任的 Microsoft 服务访问此存储帐户”选项。 此选项仅允许特定搜索服务实例（对存储帐户(强身份验证)具有基于适宜角色的访问权限）访问存储帐户中的数据，即使存储帐户是通过 IP 防火墙规则保护的也是如此。

![受信任的服务异常](media\search-indexer-howto-secure-access\exception.png "受信任的服务异常")

索引器现在能够访问存储帐户中的数据，即使存储帐户是通过 IP 防火墙规则保护的也是如此。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 存储索引器：

- [Azure Blob 索引器](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 索引器](search-howto-index-azure-data-lake-storage.md)
- [Azure 表索引器](search-howto-indexing-azure-tables.md)