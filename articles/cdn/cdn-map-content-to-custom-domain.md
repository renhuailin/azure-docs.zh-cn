---
title: 教程：将自定义域添加到终结点
titleSuffix: Azure Content Delivery Network
description: 使用本教程将自定义域添加到 Azure 内容分发网络终结点，以便在 URL 中可以看到域名。
services: cdn
author: jessie-jyy
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 04/12/2021
ms.author: yuajia
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: ee721a56da41c4a5ff9184c6bc716784f858507d
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112467628"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>教程：将自定义域添加到终结点

本教程介绍如何将自定义域添加到 Azure 内容分发网络 (CDN) 终结点。 

CDN 配置文件中的终结点名称是 azureedge.net 的子域。 默认情况下，交付内容时，URL 中包含 CDN 配置文件域。

例如，`https://contoso.azureedge.net/photo.png` 。

Azure CDN 提供了用于将自定义域与 CDN 终结点相关联的选项。 使用此选项时，会在 URL 中使用自定义域（而不是默认域）来交付内容。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 创建 CNAME DNS 记录。
> - 将自定义域与 CDN 终结点相关联。
> - 验证自定义域。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 在完成本教程中的步骤之前，先创建 CDN 配置文件和至少一个 CDN 终结点。 
    * 有关详细信息，请参阅[快速入门：创建 Azure CDN 配置文件和终结点](cdn-create-new-endpoint.md)。

* 如果没有自定义域，则先在域提供商那里购买一个。 
    * 有关详细信息，请参阅[购买自定义域名](../app-service/manage-custom-dns-buy-domain.md)。

* 如果使用 Azure 托管 [DNS 域](../dns/dns-overview.md)，则将自定义域委托到 Azure DNS。 
    * 有关详细信息，请参阅 [向 Azure DNS 委派域](../dns/dns-delegate-domain-azure-dns.md)。

* 如果使用域提供商来处理 DNS 域，请转到[创建 CNAME DNS 记录](#create-a-cname-dns-record)。


## <a name="create-a-cname-dns-record"></a>创建 CNAME DNS 记录

在将自定义域与 Azure CDN 终结点结合使用之前，必须先在 Azure DNS 或 DNS 提供商的配合下创建一个规范名称 (CNAME) 记录，使之指向 CDN 终结点。 

CNAME 记录是一种 DNS 记录，用于将源域名映射到目标域名。 

就 Azure CDN 来说，源域名是自定义域名，目标域名是 CDN 终结点主机名。 

Azure CDN 验证 CNAME 记录后，将寻址到源自定义域的流量路由到目标 CDN 终结点主机名。

一个自定义域及其子域一次只能与一个终结点相关联。 

将同一自定义域中不同子域的多个 CNAME 记录用于不同的 Azure 服务。

还可以将包含不同子域的自定义域映射到同一 CDN 终结点。

> [!NOTE]
> 本教程使用 CNAME 记录类型。 如果使用 A 或 AAAA 记录类型，请遵循以下相同步骤，并将 CNAME 替换为所选的记录类型。

---
# <a name="azure-dns"></a>[**Azure DNS**](#tab/azure-dns)

Azure DNS 将别名记录用于同一订阅中的 Azure 资源。

若要为 Azure CDN 终结点添加别名记录，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在左侧菜单中，选择“所有资源”，然后为自定义域选择 Azure DNS 区域。

3. 在自定义域的 DNS 区域中，选择“+ 记录集”。

4. 在“添加记录集”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称  | 输入要用于 CDN 终结点的别名。 例如 **www**。 |
    | 类型  | 选择“CNAME”。 |
    | 别名记录集 | 选择 **“是”** 。 |
    | 别名类型 | 选择“Azure 资源”。 |
    | 选择订阅 | 选择订阅。 |
    | Azure 资源 | 选择 Azure CDN 终结点。 |

5. 将记录的 TTL 更改为你的值。

6. 选择“确定”。

# <a name="dns-provider"></a>[DNS 提供程序](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>映射临时的 cdnverify 子域

映射现有的生产环境中的域时，有一些特殊注意事项。 

在 Azure 门户中注册自定义域时，该域可能会出现短暂的停机现象。

为了避免 Web 流量中断，请先将自定义域映射到包含 Azure cdnverify 子域的 CDN 终结点主机名。 此流程可创建临时的 CNAME 映射。 

使用此方法，用户可以在进行 DNS 映射时访问域，不会出现中断现象。 

如果无需考虑生产停机，可以将自定义域直接映射到 CDN 终结点。 请转到[映射永久自定义域](#map-the-permanent-custom-domain)。

若要使用 cdnverify 子域创建 CNAME 记录，请执行以下操作：

1. 登录到你的自定义域的 DNS 提供商的网站。

2. 为自定义域创建一个 CNAME 记录条目并完成各个字段，如下表所示（字段名称可能有所不同）：

    | 源                    | 类型  | 目标                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify. www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - 来源：采用 cdnverify.\<custom-domain-name> 格式输入自定义域名，包括 cdnverify 子域。
        - 例如 cdnverify. www.contoso.com

    - 键入：输入或选择 CNAME。

    - 目标：采用 cdnverify.\<endpoint-name>.azureedge.net 格式输入 CDN 终结点主机名，包括 cdnverify 子域。 
        - 例如 cdnverify.contoso.azureedge.net

3. 保存更改。

## <a name="map-the-permanent-custom-domain"></a>映射永久自定义域

在本部分中，将永久自定义域映射到 CDN 终结点。 

若要创建自定义域的 CNAME 记录，请执行以下操作：

1. 登录到你的自定义域的域提供商的网站。

2. 为自定义域创建一个 CNAME 记录条目并完成各个字段，如下表所示（字段名称可能有所不同）：

    | 源          | 类型  | 目标           |
    |-----------------|-------|-----------------------|
    | [www.contoso.com](www.contoso.com) | CNAME | contoso.azureedge.net |

    - 来源：输入自定义域名。
        - 例如 www.contoso.com

    - 键入：输入或选择 CNAME。

    - 目标：采用 \<endpoint-name>.azureedge.net 格式输入 CDN 终结点主机名。 
        - 例如 contoso.azureedge.net

3. 保存所做更改。

4. 如果此前已创建临时的 cdnverify 子域 CNAME 记录，请将其删除。 

5. 如果是在生产环境中第一次使用此自定义域，请按[将自定义域与 CDN 终结点相关联](#associate-the-custom-domain-with-your-cdn-endpoint)和[验证自定义域](#verify-the-custom-domain)所述步骤进行操作。

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>将自定义域与 CDN 终结点相关联

注册自定义域以后，即可将其添加到 CDN 终结点。 


---
# <a name="azure-portal"></a>[**Azure 门户**](#tab/azure-portal)

1. 登录到 [Azure 门户](https://portal.azure.com/)，浏览到 CDN 配置文件，其中包含需要映射到自定义域的终结点。
    
2. 在“CDN 配置文件”页上，选择要与自定义域关联的 CDN 终结点。 

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="CDN 终结点选择" border="true":::
    
3. 选择“+ 自定义域”。 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="“添加自定义域”按钮" border="true":::

4. 在“添加自定义域”中，已预填充“终结点主机名”，它派生自 CDN 终结点 URL：\<endpoint-hostname>.azureedge.net  。 无法进行更改。

5. 对于 **自定义主机名**，请输入自定义域（包括子域），以用作 CNAME 记录的源域。 
    1. 例如 www.contoso.com 或 cdn.contoso.com 。 请勿使用 cdnverify 子域名称。

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="添加自定义域" border="true":::

6. 选择 **添加** 。

   Azure 会验证所输入的自定义域名是否存在 CNAME 记录。 如果该 CNAME 正确，会验证自定义域。 

   新的自定义域设置传播到所有 CDN 边缘节点可能需要一些时间： 
    - 对于 **Microsoft 推出的 Azure CDN 标准版** 配置文件，传播通常可在 10 分钟内完成。 
    - 对于 **Akamai 的 Azure CDN 标准版** 配置文件，传播通常可在一分钟内完成。 
    - 对于“Verizon 提供的 Azure CDN 标准版”  和“Verizon 提供的 Azure CDN 高级版”  配置文件，传播通常在 10 分钟内完成。   

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

1. 登录到 Azure PowerShell：

```azurepowershell-interactive
    Connect-AzAccount

```
2. 使用 [AzCdnCustomDomain](/powershell/module/az.cdn/new-azcdncustomdomain) 将自定义域映射到 CDN 终结点。 

    * 将 myendpoint8675.azureedge.net 替换为你的终结点 URL。
    * 将 myendpoint8675 替换为 CDN 终结点名称。
    * 将 www.contoso.com 替换为你的自定义域名。
    * 将 myCDN 替换为你的 CDN 配置文件名称。
    * 将 myResourceGroupCDN 替换为你的资源组名称。

```azurepowershell-interactive
    $parameters = @{
        Hostname = 'myendpoint8675.azureedge.net'
        EndPointName = 'myendpoint8675'
        CustomDomainName = 'www.contoso.com'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    New-AzCdnCustomDomain @parameters
```

Azure 会验证所输入的自定义域名是否存在 CNAME 记录。 如果该 CNAME 正确，会验证自定义域。 

   新的自定义域设置传播到所有 CDN 边缘节点可能需要一些时间： 

- 对于 **Microsoft 推出的 Azure CDN 标准版** 配置文件，传播通常可在 10 分钟内完成。 
- 对于 **Akamai 的 Azure CDN 标准版** 配置文件，传播通常可在一分钟内完成。 
- 对于“Verizon 提供的 Azure CDN 标准版”  和“Verizon 提供的 Azure CDN 高级版”  配置文件，传播通常在 10 分钟内完成。   


---
## <a name="verify-the-custom-domain"></a>验证自定义域

完成自定义域的注册后，请验证该自定义域是否引用 CDN 终结点。
 
1. 确保在终结点缓存了公共内容。 例如，如果 CDN 终结点与某个存储帐户相关联，则 Azure CDN 会将内容缓存在公共容器中。 若要测试自定义域，请将容器设置为允许公共访问，并至少包含一个文件。

2. 在浏览器中，使用自定义域导航到文件的地址。 例如，如果自定义域为 `www.contoso.com`，则缓存的文件的 URL 应类似于：`http://www.contoso.com/my-public-container/my-file.jpg`。 验证结果是否与直接在 \<endpoint-hostname>.azureedge.net 上访问 CDN 终结点时的结果相同。

## <a name="clean-up-resources"></a>清理资源

---
# <a name="azure-portal"></a>[**Azure 门户**](#tab/azure-portal-cleanup)

如果不再需要将终结点与自定义域相关联，可通过以下步骤删除自定义域：
 
1. 请转到 DNS 提供程序，删除自定义域的 CNAME 记录，或将自定义域的 CNAME 记录更新为非 Azure CDN 终结点。

    > [!Important]
    > 为防止无关联的 DNS 条目及其产生的安全性风险，自 2021 年 4 月 9 日起，Azure CDN 要求先删除 Azure CDN 终结点的 CNAME 记录，然后才能删除这些资源。 资源包括 Azure CDN 自定义域、Azure CDN 配置文件/终结点或启用了 Azure CDN 自定义域的 Azure 资源组。

2. 在 CDN 配置文件中，选择其自定义域需要删除的终结点。

3. 在“终结点”页的“自定义域”下，  右键单击要删除的自定义域，然后从上下文菜单中选择“删除”。  选择 **“是”** 。

   自定义域于是与 CDN 终结点解除关联。

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell-cleanup)

如果不再需要将终结点与自定义域相关联，可通过以下步骤删除自定义域：

1. 请转到 DNS 提供程序，删除自定义域的 CNAME 记录，或将自定义域的 CNAME 记录更新为非 Azure CDN 终结点。

    > [!Important]
    > 为防止无关联的 DNS 条目及其产生的安全性风险，自 2021 年 4 月 9 日起，Azure CDN 要求先删除 Azure CDN 终结点的 CNAME 记录，然后才能删除这些资源。 资源包括 Azure CDN 自定义域、Azure CDN 配置文件/终结点或启用了 Azure CDN 自定义域的 Azure 资源组。

2. 使用 [AzCdnCustomDomain](/powershell/module/az.cdn/remove-azcdncustomdomain) 从终结点中删除自定义域：

    * 将 myendpoint8675 替换为 CDN 终结点名称。
    * 将 www.contoso.com 替换为你的自定义域名。
    * 将 myCDN 替换为你的 CDN 配置文件名称。
    * 将 myResourceGroupCDN 替换为你的资源组名称。

    ```azurepowershell-interactive
        $parameters = @{
            CustomDomainName = 'www.contoso.com'
            EndPointName = 'myendpoint8675'
            ProfileName = 'myCDN'
            ResourceGroupName = 'myResourceGroupCDN'
        }
        Remove-AzCdnCustomDomain @parameters
    ```
---

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> - 创建 CNAME DNS 记录。
> - 将自定义域与 CDN 终结点相关联。
> - 验证自定义域。

转到下一教程，了解如何在 Azure CDN 自定义域上配置 HTTPS。

> [!div class="nextstepaction"]
> [教程：在 Azure CDN 自定义域中配置 HTTPS](cdn-custom-ssl.md)
