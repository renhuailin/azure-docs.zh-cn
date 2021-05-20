---
title: 使用受信任的服务异常对 Azure 存储进行索引器访问
titleSuffix: Azure Cognitive Search
description: 允许 Azure 认知搜索中的索引器访问安全地存储在 Azure 存储中的数据。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92101369"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>使用受信任的服务异常对 Azure 存储进行索引器访问（Azure 认知搜索）

Azure 认知搜索服务中用于访问 Azure 存储帐户中的数据的索引器可以利用[受信任的服务异常](../storage/common/storage-network-security.md#exceptions)功能来安全地访问数据。 此机制为无法授予[使用 IP 防火墙规则进行索引器访问](search-indexer-howto-access-ip-restricted.md)的客户提供了一种简单、安全且免费的替代方法来访问存储帐户中的数据。

> [!NOTE]
> 支持通过受信任的服务异常访问存储帐户中的数据仅限于 Azure Blob 存储和 Azure Data Lake Gen2 存储。 不支持 Azure 表存储。

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>步骤 1：使用托管标识配置连接

按照[使用托管标识设置到 Azure 存储帐户的连接](search-howto-managed-identities-storage.md)中的说明进行操作。 完成后，你将搜索服务作为受信任的服务向 Azure Active Directory 注册，并且你将在 Azure 存储中具有授予的权限，以便为搜索标识提供访问数据或信息的特定权限。

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>步骤 2：允许受信任的 Microsoft 服务访问存储帐户

在 Azure 门户中，导航到存储帐户的“防火墙和虚拟网络”选项卡。 确保已选中“允许受信任的 Microsoft 服务访问此存储帐户”选项。 此选项仅允许特定搜索服务实例（对存储帐户(强身份验证)具有基于适宜角色的访问权限）访问存储帐户中的数据，即使存储帐户是通过 IP 防火墙规则保护的也是如此。

![受信任的服务异常](media\search-indexer-howto-secure-access\exception.png "受信任的服务异常")

索引器现在能够访问存储帐户中的数据，即使存储帐户是通过 IP 防火墙规则保护的也是如此。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 存储索引器：

- [Azure Blob 索引器](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 索引器](search-howto-index-azure-data-lake-storage.md)
- [Azure 表索引器](search-howto-indexing-azure-tables.md)