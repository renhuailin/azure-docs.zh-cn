---
title: 网络隔离
description: 用户可以限制对 QnA Maker 资源的公共访问。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 8fe8c07866b23e5d990b71bfc9cd556c338634d3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203362"
---
# <a name="recommended-settings-for-network-isolation"></a>用于网络隔离的推荐设置

你应按照以下步骤来限制对 QnA Maker 资源的公共访问。 通过 [配置虚拟网络](../../cognitive-services-virtual-networks.md?tabs=portal)来保护认知服务资源免受公共访问。

## <a name="restrict-access-to-cognitive-search-resource"></a>限制对认知搜索资源的访问

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

将认知搜索配置为 VNET 中的专用终结点。 如果在创建 QnA Maker 资源的过程中创建搜索实例，则可以强制认知搜索支持完全在客户 VNet 中创建的专用终结点配置。

必须在同一区域中创建所有资源，才能使用专用终结点。

* QnA Maker 资源
* 新建认知搜索资源
* 新建虚拟网络资源

在 [Azure 门户](https://portal.azure.com)中完成以下步骤：

1. 创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。
2. 使用终结点连接创建新的认知搜索资源 (数据) 设置为 " _专用_"。 在步骤1中创建的 QnA Maker 资源所在的同一区域中创建资源。 详细了解如何 [创建认知搜索资源](../../../search/search-create-service-portal.md)，然后使用此链接直接前往 [资源的创建页面](https://ms.portal.azure.com/#create/Microsoft.Search)。
3. 创建新的 [虚拟网络资源](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM)。
4. 在此过程的步骤1中创建的应用服务资源上配置 VNET。 在 VNET 中为在步骤2中创建的新认知搜索资源创建新的 DNS 条目。 到认知搜索的 IP 地址。
5. [将应用服务关联到步骤2中创建的新认知搜索资源](../how-to/set-up-qnamaker-service-azure.md) 。 然后，你可以删除在步骤1中创建的原始认知搜索资源。
    
在 [QnA Maker 门户](https://www.qnamaker.ai/)中，创建第一个知识库。

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

创建 Azure 搜索资源的[专用终结点](../reference-private-endpoint.md)。

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>限制对应用服务 (QnA 运行时的访问) 

你可以将 Ip 添加到应用服务允许列表，以限制访问或配置应用服务环境以托管 QnA Maker 应用服务。

#### <a name="add-ips-to-app-service-allowlist"></a>将 Ip 添加到应用服务允许列表

1. 仅允许来自认知服务 Ip 的流量。 服务标记中已包含这些项 `CognitiveServicesManagement` 。 这对于创作 Api (创建/更新 KB) ，以相应地调用应用服务和更新 Azure 搜索服务是必需的。 查看 [有关服务标记的详细信息。](../../../virtual-network/service-tags-overview.md)
2. 确保还允许使用其他入口点（如 Azure Bot 服务、QnA Maker 门户等）进行预测 "GenerateAnswer" API 访问。
3. 请按照以下步骤将 IP 地址范围添加到允许列表：

   1. 下载 [所有服务标记的 IP 范围](https://www.microsoft.com/download/details.aspx?id=56519)。
   2. 选择 "CognitiveServicesManagement" 的 Ip。
   3. 导航到应用服务资源的 "网络" 部分，然后单击 "配置访问限制" 选项，将 Ip 添加到允许列表。

    ![入站端口例外](../media/inbound-ports.png)

此外，我们还提供了一个自动化脚本，为你的应用服务执行相同的操作。 可在 GitHub 上找到 [用于配置允许列表的 PowerShell 脚本](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) 。 需要输入订阅 id、资源组和实际应用服务名称作为脚本参数。 运行脚本会自动将 Ip 添加到应用服务允许列表中。

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>配置应用服务环境以承载 QnA Maker 应用服务
    
应用服务环境 (ASE) 可用于托管 QnA Maker 应用服务。 请执行以下步骤：

1. 创建应用服务环境并将其标记为 "外部"。 请按照 [本教程](../../../app-service/environment/create-external-ase.md) 中的说明进行操作。
2.  在应用服务环境中创建应用服务。
    1. 检查应用服务的配置，并将 "PrimaryEndpointKey" 添加为应用程序设置。 "PrimaryEndpointKey" 的值应设置为 " \<app-name\> -PrimaryEndpointKey"。 应用程序名称在应用服务 URL 中定义。 例如，如果应用服务 URL 为 "mywebsite.myase.p.azurewebsite.net"，则应用名称为 "mywebsite"。 在这种情况下，应将 "PrimaryEndpointKey" 的值设置为 "mywebsite-PrimaryEndpointKey"。
    2. 创建 Azure 搜索服务。
    3. 确保已正确配置 Azure 搜索和应用设置。 
          请按照本 [教程](../reference-app-service.md?tabs=v1#app-service)操作。
3.  更新与应用服务环境关联的网络安全组
    1. 按照您的要求更新预先创建的入站安全规则。
    2. 添加一个新的入站安全规则，将源作为 "服务标记"，将源服务标记添加为 "CognitiveServicesManagement"。
4.  使用 Azure 资源管理器 (Cognitiveservices account/accounts) 创建 QnA Maker 认知服务实例，其中 QnA Maker 终结点应设置为上面 (https://mywebsite.myase.p.azurewebsite.net) 创建的应用服务终结点。
    
---
