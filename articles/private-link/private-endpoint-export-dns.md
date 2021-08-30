---
title: 使用 Azure 门户导出专用终结点的 DNS 记录
titleSuffix: Azure Private Link
description: 本教程介绍如何在 Azure 门户中导出专用终结点的 DNS 记录。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: how-to
ms.date: 07/25/2021
ms.custom: how-to
ms.openlocfilehash: d4ad1fd995bfd4f1565215491037e6450bd65bf2
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713075"
---
# <a name="export-dns-records-for-a-private-endpoint-using-the-azure-portal"></a>使用 Azure 门户导出专用终结点的 DNS 记录

Azure 中的专用终结点需要 DNS 记录来对终结点进行名称解析。 DNS 记录会为已配置的资源解析终结点的专用 IP 地址。 若要导出终结点的 DNS 记录，请使用门户中的专用链接中心。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 订阅中配置的专用终结点。 对于本文中的示例，会使用 Azure Web 应用的专用终结点。 有关创建 Web 应用的专用终结点的详细信息，请参阅[教程：使用 Azure 专用终结点连接到 Web 应用](tutorial-private-endpoint-webapp-portal.md)。

## <a name="export-endpoint-dns-records"></a>导出终结点 DNS 记录

在本部分，你将登录到 Azure 门户并搜索专用链接中心。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“专用链接”。

3. 选择“专用链接”。

4. 在专用链接中心，选择“专用终结点”。

    :::image type="content" source="./media/private-endpoint-export-dns/private-link-center.png" alt-text="在专用链接中心选择“专用终结点”":::

5. 在“专用终结点”中，选择要导出 DNS 记录的终结点。 选择“下载主机文件”，以主机文件格式下载终结点 DNS 记录。
    
    :::image type="content" source="./media/private-endpoint-export-dns/download-host-file.png" alt-text="下载终结点 DNS 记录":::

6. 下载的主机文件记录将如下所示：

    ```text
    # Exported from the Azure portal "2021-07-26 11:26:03Z"
    # Private IP    FQDN    Private Endpoint Id
    10.1.0.4    mywebapp8675.scm.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    10.1.0.4    mywebapp8675.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    ```

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 专用链接和 DNS，请参阅 [Azure 专用终结点 DNS 配置](private-endpoint-dns.md)。

有关 Azure 专用链接的详细信息，请参阅：

* [什么是 Azure 专用链接？](private-link-overview.md)
* [什么是 Azure 专用链接服务？](private-link-service-overview.md)
* [Azure 专用链接常见问题解答 (FAQ)](private-link-faq.yml)