---
title: 网络隔离
description: 用户可以限制对 QnA Maker 资源的公共访问。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 91bfa04c9fab179cf3935e703f13004f3aadd170
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220007"
---
# <a name="recommended-settings-for-network-isolation"></a>网络隔离的建议设置

请按照以下步骤来限制对 QnA Maker 资源的公共访问。 通过[配置虚拟网络](../../cognitive-services-virtual-networks.md?tabs=portal)来保护认知服务资源免受公共访问。

## <a name="restrict-access-to-app-service-qna-runtime"></a>限制对应用服务的访问（QnA 运行时）

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

你可以使用 ServiceTag `CognitiveServicesMangement` 来限制对应用服务或 ASE (应用服务环境) 网络安全组绑定内规则的入站访问。可在 [虚拟网络服务标记文章](../../../virtual-network/service-tags-overview.md)中了解有关服务标记的详细信息。 

### <a name="regular-app-service"></a>常规应用服务

1. 在 Azure 门户中打开 Cloud Shell (PowerShell)。
2. 在页面底部的 PowerShell 窗口中运行以下命令：

```ps
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "<resource group name>" -WebAppName "<app service name>" -Name "cognitive services Tag" -Priority 100 -Action Allow -ServiceTag "CognitiveServicesManagement" 
```
3.  验证“网络”选项卡的“访问限制”部分中是否存在添加的访问规则：   

    > [!div class="mx-imgBorder"]
    > [ ![访问限制规则的屏幕截图]( ../media/network-isolation/access-restrictions.png) ](  ../media/network-isolation/access-restrictions.png#lightbox)

4. 若要在 https://qnamaker.ai 门户中访问测试窗格，请添加要从中访问门户的计算机的公共 IP 地址。  从“访问限制”页中选择“添加规则”并允许访问你客户端 IP。  

    > [!div class="mx-imgBorder"]
    > [![添加了公共 IP 地址的访问限制规则的屏幕截图]( ../media/network-isolation/public-address.png)](  ../media/network-isolation/public-address.png#lightbox)

### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>配置应用服务环境以承载 QnA Maker 应用服务

应用服务环境 (ASE) 可用于托管 QnA Maker 应用服务实例。 请遵循以下步骤进行配置：

1. 创建[新 Azure 认知搜索资源](https://ms.portal.azure.com/#create/Microsoft.Search)。
2. 使用应用服务创建外部 ASE。
    - 按照此[应用服务快速入门](../../../app-service/environment/create-external-ase.md#create-an-ase-and-an-app-service-plan-together)中的说明操作。 此过程可能需要长达 1 到 2 个小时的时间。
    - 最后，将出现类似于以下内容的应用服务终结点：`https://<app service name>.<ASE name>.p.azurewebsite.net`。 
    - 示例： `https:// mywebsite.myase.p.azurewebsite.net`  
3. 添加以下应用服务配置：
    
    | 名称                       | 值                                                     |
    |:---------------------------|:----------------------------------------------------------| 
    | PrimaryEndpointKey         | `<app service name>-PrimaryEndpointKey`                   | 
    | AzureSearchName            | `<Azure Cognitive Search Resource Name from step #1>`     | 
    | AzureSearchAdminKey        | `<Azure Cognitive Search Resource admin Key from step #1>`| 
    | QNAMAKER_EXTENSION_VERSION | `latest`                                                  |
    | DefaultAnswer              | `no answer found`                                         |

4. 在应用服务上添加 CORS 源“*”以允许访问 https://qnamaker.ai 门户测试窗格。 “CORS”位于“应用服务”窗格中的 API 标头下。

    > [!div class="mx-imgBorder"]
    > [![应用服务 UI 中 CORS 接口的屏幕截图]( ../media/network-isolation/cross-orgin-resource-sharing.png)](  ../media/network-isolation/cross-orgin-resource-sharing.png#lightbox)

5. 使用 Azure 资源管理器创建 QnA Maker 认知服务实例 (Microsoft.CognitiveServices/accounts)。 应将 QnA Maker 终结点设置为上面创建的应用服务终结点 (`https:// mywebsite.myase.p.azurewebsite.net`)。 下面是一个[示例 Azure 资源管理器模板，可供参考](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/QnAMakerASEArmTemplate)。

### <a name="related-questions"></a>相关问题

#### <a name="can-qna-maker-be-deployed-to-an-internal-ase"></a>是否可以将 QnA Maker 部署到内部 ASE？ 

使用外部 ASE 的主要原因是 QnAMaker 服务后端（创作 api）可以通过 Internet 访问应用服务。 但仍可以通过添加入站访问限制来保护应用服务，以便仅允许来自与 `CognitiveServicesManagement` 服务标记相关联的地址的连接。

如果仍要使用内部 ASE，则需要通过应用网关 DNS TLS/SSL 证书在公共域上的 ASE 中公开该特定 QnA Maker 应用。有关详细信息，请参阅这篇[有关应用服务 Enterprise 部署的文章](/azure/architecture/reference-architectures/enterprise-integration/ase-standard-deployment)。

    
# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

应用服务未部署自定义问答。

---

## <a name="restrict-access-to-cognitive-search-resource"></a>限制对认知搜索资源的访问

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

在创建 QnA Maker 资源后，可以通过专用终结点隔离认知搜索实例。 使用以下步骤锁定访问权限：

1. 创建新的[虚拟网络 (VNet)](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM)，或使用 ASE（应用服务环境）的现有 VNet。
2. 打开 VNet 资源，然后在“子网”选项卡下创建两个子网。 一个用于应用服务 (appservicesubnet)，另一个子网 (searchservicesubnet) 用于认知搜索资源，无委派。  

    > [!div class="mx-imgBorder"]
    > [![虚拟网络子网 UI 接口的屏幕截图]( ../media/network-isolation/subnets.png)](  ../media/network-isolation/subnets.png#lightbox)

3. 在认知搜索服务实例的“网络”选项卡中，将端点连接数据从公共切换到私有。 此操作是一个长时间运行的过程，可能需要长达 30 分钟的时间才能完成。

    > [!div class="mx-imgBorder"]
    > [![带有公用/专用切换按钮的网络 UI 的屏幕截图]( ../media/network-isolation/private.png)](  ../media/network-isolation/private.png#lightbox)

4. 将搜索资源切换到私有后，选择添加专用终结点。
    - “基本”选项卡：确保在搜索资源所在的同一区域中创建终结点。
    - “资源”选项卡：选择所需的 `Microsoft.Search/searchServices` 类型的搜索资源。

    > [!div class="mx-imgBorder"]
    > [![创建专用终结点 UI 窗口的屏幕截图]( ../media/network-isolation/private-endpoint.png)](  ../media/network-isolation/private-endpoint.png#lightbox)

    - “配置”选项卡：使用在步骤 2 中创建的 VNet、子网 (searchservicesubnet)。 之后，在“专用 DNS 集成”部分中，选择相应的订阅，并新建一个名为 privatelink.search.windows.net 的专用 DNS 区域。 

     > [!div class="mx-imgBorder"]
     > [![填充了子网字段的创建私有终结点 UI 窗口的屏幕截图]( ../media/network-isolation/subnet.png)](  ../media/network-isolation/subnet.png#lightbox)

    5. 为常规应用服务启用 VNET 集成。 对于 ASE，可以跳过此步骤，因为 ASE 已经有权访问 VNET。
        - 请参阅应用服务的“网络”部分，打开“VNet 集成”。 
        - 链接到在步骤 2 中创建的专用应用服务 VNet、子网 (appservicevnet)。
    
     > [!div class="mx-imgBorder"]
     > [![VNET 集成 UI 的屏幕截图]( ../media/network-isolation/integration.png)](  ../media/network-isolation/integration.png#lightbox)


# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

为 Azure 搜索资源[创建专用终结点](../reference-private-endpoint.md)。

请按照以下步骤来限制对 QnA Maker 资源的公共访问。 通过[配置虚拟网络](../../cognitive-services-virtual-networks.md?tabs=portal)来保护认知服务资源免受公共访问。

基于 VNet 限制对认知服务资源的访问权限后，可以从本地网络或本地浏览器浏览 https://qnamaker.ai 门户上的知识库。
- 授予对[本地网络](../../cognitive-services-virtual-networks.md?tabs=portal#configuring-access-from-on-premises-networks)的访问权限。
- 授予对[本地浏览器/计算机](../../cognitive-services-virtual-networks.md?tabs=portal#managing-ip-network-rules)的访问权限。
- 在“网络”选项卡的“防火墙”部分下添加计算机的公共 IP 地址。默认情况下，`portal.azure.com` 显示当前浏览器的公共 IP（选择此条目），然后选择“保存”。  

     > [!div class="mx-imgBorder"]
     > [![防火墙和虚拟网络配置 UI 的屏幕截图]( ../media/network-isolation/firewall.png)](  ../media/network-isolation/firewall.png#lightbox)

---

