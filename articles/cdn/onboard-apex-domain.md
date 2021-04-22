---
title: 将根或顶点域加入现有 Azure CDN 终结点 - Azure 门户
description: 了解如何使用 Azure 门户将根或顶点域加入现有 Azure CDN 终结点。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370032"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>将根或顶点域加入现有 Azure CDN 终结点

Azure CDN 使用 CNAME 记录来验证域所有权以加入自定义域。 CDN 不会公开与 CDN 配置文件关联的前端 IP 地址。 如果你的目的是将顶点域加入到 Azure CDN，则不能将其映射到 IP 地址。

DNS 协议会阻止在区域顶点分配 CNAME 记录。 例如，如果你的域为 `contoso.com`，则可以为 `somelabel.contoso.com` 创建 CNAME 记录，但无法为 `contoso.com` 本身创建 CNAME。 对于在 Azure CDN 后面具有负载均衡应用程序的应用程序所有者，此限制会带来问题。 由于使用 CDN 配置文件需要创建 CNAME 记录，因此无法从区域顶点指向 CDN 配置文件。

此问题可使用 Azure DNS 中的别名记录得以解决。 与 CNAME 记录不同，别名记录是在区域顶点创建的。 应用程序所有者可使用别名记录将其区域顶点记录指向具有公共终结点的 CDN 配置文件。 应用程序所有者可以指向用于其 DNS 区域中任何其他域的相同 CDN 配置文件。 例如，`contoso.com` 和 `www.contoso.com` 可以指向相同的 CDN 配置文件。 

将顶点域或根域映射到 CDN 配置文件需要 CNAME 平展或 DNS 跟踪。 这是一种机制，其中 DNS 提供程序在达到 IP 地址之前将以递归方式解析 CNAME 条目。 此功能由 Azure DNS 提供支持，用于 CDN 终结点。 

> [!NOTE]
> 还有其他一些支持 CNAME 平展或 DNS 跟踪的 DNS 提供程序，但是 Azure CDN 建议其客户使用 Azure DNS 来托管其域。

你可以使用 Azure 门户在 CDN 上加入顶点域，并通过将其与 TLS 终止证书相关联，对其启用 HTTPS。 顶点域也称为根域或裸域。

## <a name="create-an-alias-record-for-zone-apex"></a>为区域顶点创建别名记录

1. 打开要加入的域的“Azure DNS”配置。

2. 选择“+记录集”。

3. 在“添加记录集”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ------|
    | 名称 | 输入 **@** 。 |
    | 类型 | 选择 A。 |
    | 别名记录集 | 选择 **“是”** 。 |
    | 别名类型 | 选择“Azure 资源”。 |
    | 选择订阅 | 选择订阅。 |
    | Azure 资源 | 选择 CDN 终结点。 |

4. 输入“TTL”的值。

5. 选择“确定”以提交更改。

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="区域顶点的别名记录":::

6. 上述步骤将创建指向 CDN 资源的区域顶点记录。 CNAME 记录映射 cdnverify 用于在 CDN 配置文件中加入域。
    1. 例如 cdnverify.contoso.com。
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>在 CDN 上加入自定义域

注册自定义域以后，即可将其添加到 CDN 终结点。 

1. 登录到 [Azure 门户](https://portal.azure.com/)，浏览到 CDN 配置文件，其中包含需要映射到自定义域的终结点。
    
2. 在“CDN 配置文件”页上，选择要与自定义域关联的 CDN 终结点。 

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="CDN 终结点选择" border="true":::
    
3. 选择“+ 自定义域”。 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="“添加自定义域”按钮" border="true":::

4. 在“添加自定义域”中，已预填充“终结点主机名”，它派生自 CDN 终结点 URL：\<endpoint-hostname>.azureedge.net  。 无法进行更改。

5. 对于“自定义主机名”，请输入自定义根或顶点域，以用作 CNAME 记录的源域。 
    1. 例如 **contoso.com**。 请勿使用 cdnverify 子域名称。

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="添加自定义域" border="true":::

6. 选择 **添加** 。

   Azure 会验证所输入的自定义域名是否存在 CNAME 记录。 如果该 CNAME 正确，会验证自定义域。 

   新的自定义域设置传播到所有 CDN 边缘节点可能需要一些时间： 
    - 对于 **Microsoft 推出的 Azure CDN 标准版** 配置文件，传播通常可在 10 分钟内完成。 
    - 对于 **Akamai 的 Azure CDN 标准版** 配置文件，传播通常可在一分钟内完成。 
    - 对于“Verizon 提供的 Azure CDN 标准版”  和“Verizon 提供的 Azure CDN 高级版”  配置文件，传播通常在 10 分钟内完成。   

## <a name="enable-https-on-your-custom-domain"></a>在自定义域上启用 HTTPS

有关在 Azure CDN 的自定义域上启用 HTTPS 的详细信息，请参阅[教程：在 Azure CDN 自定义域上配置 HTTPS](cdn-custom-ssl.md)

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 CDN 终结点](cdn-create-new-endpoint.md)。
