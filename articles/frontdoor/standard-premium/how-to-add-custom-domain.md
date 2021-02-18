---
title: 如何将自定义域添加到 Azure 前门标准/高级 SKU 配置
description: 在本教程中，你将了解如何将自定义域加入 Azure 前门标准/高级 SKU。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 164e06024844fb5262586450b737db9c807e373a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098676"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>使用 Azure 门户在 Azure 前门标准/高级 SKU 上创建自定义域 (预览) 

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

使用 Azure 前门标准/高级版进行应用程序交付时，如果你希望自己的域名在最终用户请求中可见，则必须提供自定义域。 使用可见的域名可以方便客户，适用于推广品牌。

创建 Azure 前门标准/高级配置文件后，默认前端主机将具有 azurefd.net 的子域。 默认情况下，当 Azure 前门标准/高级版提供来自后端的内容时，此子域将包含在 URL 中。 例如，`https://contoso-frontend.azurefd.net/activeusers.htm`。 为方便起见，Azure Front Door 提供了用于将自定义域与默认主机相关联的选项。 使用此选项，可以在 URL 中使用自定义域传递内容，而不是使用 Azure 前门标准/高级所有者域名。 例如，https://www.contoso.com/photo.png。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件
* 在完成本教程中的步骤之前，必须先创建一个 Front Door。 有关详细信息，请参阅 [快速入门：创建前门标准/高级版](create-front-door-portal.md)。

* 如果还没有自定义域，则必须先使用域提供商购买一个。 有关示例，请参阅[购买自定义域名](../../app-service/manage-custom-dns-buy-domain.md)。

* 如果要使用 Azure 来托管 [DNS 域](../../dns/dns-overview.md)，则必须将域提供商的域名系统 (DNS) 委托给 Azure DNS。 有关详细信息，请参阅 [向 Azure DNS 委派域](../../dns/dns-delegate-domain-azure-dns.md)。 否则，如果你使用域提供程序来处理 DNS 域，则必须通过输入提示的 DNS TXT 记录手动验证域。

## <a name="add-a-new-custom-domain"></a>添加新的自定义域

自定义域通过门户中的 "域" 部分进行管理。 在与终结点建立关联之前，可以创建并验证自定义域。 自定义域及其子域一次只能与一个终结点相关联。 但是，可以使用来自同一自定义域的不同子域来实现不同的前门。 你还可以将具有不同子域的自定义域映射到同一个前门终结点。

1. 在 Azure 前门配置文件的 "设置" 下，选择 " *域*  "，然后选择 " **添加域** " 按钮。

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="域登录页上的 &quot;添加域&quot; 按钮的屏幕截图。":::

1. 此时将显示 " **添加域** " 页，您可以在其中输入有关自定义域的信息。 你可以选择 Azure 托管的 DNS （建议使用），也可以选择使用自己的 DNS 提供程序。 如果选择 Azure 托管 DNS，请选择现有的 DNS 区域，然后选择一个自定义子域或创建一个新的子域。 如果使用的是其他 DNS 提供程序，请手动输入自定义域名。 选择 " **添加** " 以添加自定义域。

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="添加域页的屏幕截图。":::

    创建新的自定义域时，验证状态为 "已 **提交**"。

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="提交域验证状态的屏幕截图。":::

    等待验证状态更改为 " **挂起**"。 此操作可能需要几分钟的时间。

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="域验证状态挂起的屏幕截图。":::

1. 选择 **挂起** 的验证状态。 将显示一个新页，其中包含验证自定义域所需的 DNS TXT 记录信息。 TXT 记录的形式为 `_dnsauth.<your_subdomain>` 。 如果使用的是基于 Azure DNS 的区域，请选择 " **添加** " 按钮，将在 Azure DNS 区域中创建具有显示的记录值的新 TXT 记录。 如果使用的是其他 DNS 提供程序，请使用记录值手动创建名称的新 TXT 记录， `dnsauth.<your_subdomain>` 如页中所示。

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="&quot;验证自定义域&quot; 页的屏幕截图。":::

1. 选择 "刷新状态"。 使用 DNS TXT 记录验证域后，验证状态将更改为 " **已验证**"。 此操作可能需要几分钟的时间才能验证。

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="已验证的自定义域屏幕截图。":::

1. 关闭页面以返回到 "自定义域列表" 登录页。 自定义域的预配状态应更改为 "已 **设置** " 并且验证状态应更改为 " **已批准**"。

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="已设置和已批准状态的屏幕截图。":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>将自定义域与前门终结点相关联

验证自定义域后，可以将其添加到 Azure 前门标准/高级终结点。

1. 自定义域经过验证后，可以将其关联到现有的 Azure 前门标准/高级终结点和路由。 选择 " **终结点关联** " 链接以打开 " **关联终结点和路由** " 页。 选择要与相关联的终结点和路由。 然后选择 " **关联**"。 关联操作完成后关闭页面。

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="&quot;关联终结点和路由&quot; 页的屏幕截图。":::

    终结点关联状态应更改，以反映自定义域当前关联的终结点。 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="终结点关联链接的屏幕截图。":::

1. 选择 "DNS 状态" 链接。

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="DNS 状态链接的屏幕截图。":::

1. " **添加或更新 CNAME 记录** " 页将出现，并显示必须在流量开始流动之前提供的 cname 记录信息。 如果使用 Azure DNS 托管区域，则可以通过选择页面上的 " **添加** " 按钮来创建 CNAME 记录。 如果使用的是其他 DNS 提供程序，则必须手动输入 CNAME 记录名称和值，如页中所示。

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="添加或更新 CNAME 记录的屏幕截图。":::

1. 创建 CNAME 记录并且自定义域关联到 Azure 前门终结点完成后，流量将开始流动。

    > [!NOTE]
    > 如果启用了 HTTPS，证书预配和传播可能需要几分钟的时间，因为传播正在全部边缘位置进行。 

## <a name="verify-the-custom-domain"></a>验证自定义域

验证并关联自定义域后，验证是否已将自定义域正确引用到你的终结点。

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="已验证和关联的自定义域的屏幕截图。":::

最后，验证应用程序内容是否是使用浏览器提供的。

## <a name="next-steps"></a>后续步骤

若要了解如何为自定义域启用 HTTPS，请继续学习下一教程。

> [!div class="nextstepaction"]
> [为自定义域启用 HTTPS]()
