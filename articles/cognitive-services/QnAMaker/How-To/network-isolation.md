---
title: 网络隔离
description: 用户可以限制对 QnA Maker 资源的公共访问。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: fa24347c8fcc0550dc6dc86c96624d1b1f6dcf25
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376316"
---
# <a name="recommended-settings-for-network-isolation"></a>网络隔离的建议设置

应按照以下步骤来限制对 QnA Maker 资源的公共访问。 通过[配置虚拟网络](../../cognitive-services-virtual-networks.md?tabs=portal)来保护认知服务资源免受公共访问。

## <a name="restrict-access-to-app-service-qna-runtime"></a>限制对应用服务的访问（QnA 运行时）

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

可以将 IP 添加到应用服务允许列表来限制访问，或配置应用服务环境以承载 QnA Maker 应用服务。

#### <a name="add-ips-to-app-service-allow-list"></a>将 IP 添加到应用服务允许列表

1. 仅允许来自认知服务 IP 的流量。 服务标记 `CognitiveServicesManagement` 中已包含这些项。 这是创作 API（创建/更新 KB）调用应用服务和相应地更新 Azure 搜索服务所必需的。 查看[有关服务标记的详细信息。](../../../virtual-network/service-tags-overview.md)
2. 确保还允许其他入口点（如 Azure 机器人服务、QnA Maker 门户等）来预测“GenerateAnswer”API 访问。
3. 请按照以下步骤将 IP 地址范围添加到允许列表：

   1. 下载[所有服务标记的 IP 范围](https://www.microsoft.com/download/details.aspx?id=56519)。
   2. 选择“CognitiveServicesManagement”的 IP。
   3. 导航到应用服务资源的“网络”部分，然后单击“配置访问限制”选项，将 IP 添加到允许列表。

此外，我们还提供了一个自动化脚本，为你的应用服务执行相同操作。 可在 GitHub 上找到[用于配置允许列表的 PowerShell 脚本](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1)。 需要输入订阅 ID、资源组和实际应用服务名称作为脚本参数。 运行脚本会自动将 IP 添加到应用服务允许列表。

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>配置应用服务环境以承载 QnA Maker 应用服务
    
应用服务环境 (ASE) 可用于托管 QnA Maker 应用服务。 请执行以下步骤：

1. 创建应用服务环境并将其标记为“外部”。 请按照[本教程](../../../app-service/environment/create-external-ase.md)中的说明操作。
2.  在应用服务环境中创建应用服务。
    1. 检查应用服务的配置，并将“PrimaryEndpointKey”添加为应用程序设置。 “PrimaryEndpointKey”的值应设置为“\<app-name\> -PrimaryEndpointKey”。 应用名称在应用服务 URL 中定义。 例如，如果应用服务 URL 为“mywebsite.myase.p.azurewebsite.net”，则应用名称为“mywebsite”。 在这种情况下，应将“PrimaryEndpointKey”的值设置为“mywebsite-PrimaryEndpointKey”。
    2. 创建 Azure 搜索服务。
    3. 确保已正确配置 Azure 搜索和应用设置。 
          请按本[教程](../reference-app-service.md?tabs=v1#app-service)的说明操作。
3.  更新与应用服务环境关联的网络安全组
    1. 按照你的要求更新预先创建的入站安全规则。
    2. 添加一个新的入站安全规则，其中源为“服务标记”，源服务标记为“CognitiveServicesManagement”。
       
    ![入站端口例外](../media/inbound-ports.png)

4.  使用 Azure 资源管理器创建 QnA Maker 认知服务实例 (Microsoft.CognitiveServices/accounts)，其中 QnA Maker 终结点应设置为上面创建的应用服务终结点 (https:// mywebsite.myase.p.azurewebsite.net)。
    
# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

应用服务未部署自定义问答。

---

## <a name="restrict-access-to-cognitive-search-resource"></a>限制对认知搜索资源的访问

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

在创建 QnA Maker 资源后，可以通过专用终结点隔离认知搜索实例。 专用终结点连接需要一个 VNet，通过该 VNet 可以访问搜索服务实例。 

如果 QnA Maker 应用服务被限制为使用某个应用服务环境，请使用相同的 VNet 创建到认知搜索实例的专用终结点连接。 在 VNet 中创建新 DNS 条目，以将认知搜索终结点映射到认知搜索专用终结点 IP 地址。 

如果没有为 QnAMaker 应用服务使用应用服务环境，请先创建新的 VNet 资源，然后创建与认知搜索实例的专用终结点连接。 在这种情况下，需要将 QnA Maker 应用服务[与 VNet 集成](../../../app-service/web-sites-integrate-with-vnet.md)，以便连接到认知搜索实例。 

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

为 Azure 搜索资源[创建专用终结点](../reference-private-endpoint.md)。

---

