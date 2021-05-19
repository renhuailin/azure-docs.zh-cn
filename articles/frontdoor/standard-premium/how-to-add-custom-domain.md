---
title: 如何将自定义域添加到 Azure Front Door 标准版/高级版 SKU 配置
description: 本教程介绍了如何将自定义域载入到 Azure Front Door 标准版/高级版 SKU 中。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 69e216c8893f9361a18354e5d165ecc0499601aa
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387915"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>使用 Azure 门户在 Azure Front Door 标准版/高级版 SKU（预览版）上创建自定义域

> [!Note]
> 本文档适用于 Azure Front Door 标准版/高级版（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

使用 Azure Front Door 标准版/高级版进行应用程序交付时，如果希望自己的域名在最终用户请求中可见，则必须使用自定义域。 使用可见的域名可以方便客户，适用于推广品牌。

创建 Azure Front Door 标准版/高级版配置文件后，默认前端主机将拥有 azurefd.net 的子域。 默认情况下，当 Azure Front Door 标准版/高级版提供来自后端的内容时，此子域将包含在 URL 中。 例如，`https://contoso-frontend.azurefd.net/activeusers.htm`。 为方便起见，Azure Front Door 提供了用于将自定义域与默认主机相关联的选项。 在此选项下，可以使用 URL 中的自定义域，而不是 Azure Front Door 标准版/高级版所有的域名来传送内容。 例如，“https://www.contoso.com/photo.png”。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件
* 在完成本教程中的步骤之前，必须先创建一个 Front Door。 有关详细信息，请参阅[快速入门：创建 Front Door 标准版/高级版](create-front-door-portal.md)。

* 如果没有自定义域，则必须先在域提供商那里购买一个。 有关示例，请参阅[购买自定义域名](../../app-service/manage-custom-dns-buy-domain.md)。

* 如果使用 Azure 来托管 [DNS 域](../../dns/dns-overview.md)，必须将域提供商的域名系统 (DNS) 委托给 Azure DNS。 有关详细信息，请参阅 [向 Azure DNS 委派域](../../dns/dns-delegate-domain-azure-dns.md)。 否则，如果使用域提供商来处理 DNS 域，则必须输入提示的 DNS TXT 记录以手动验证域。

## <a name="add-a-new-custom-domain"></a>添加新的自定义域

> [!NOTE]
> 在公共预览版中，Azure Front Door 标准版/高级版上不支持使用 Azure DNS 创建 Apex 域。 还有其他 DNS 提供程序支持 CNAME 平展或 DNS 跟踪，将允许 APEX 域用于 Azure Front Door 标准版/高级版。

自定义域通过门户中的“域”部分进行管理。 在与终结点建立关联之前，可以创建并验证自定义域。 一个自定义域及其子域一次只能与一个终结点相关联。 但是，可以将同一自定义域中的不同子域用于不同的 Front Door。 还可以将包含不同子域的自定义域映射到同一 Front Door 终结点。

1. 在 Azure Front Door 配置文件的“设置”下，选择“域”，然后选择“添加域”按钮。

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="域登录页上添加域按钮的屏幕截图。":::

1. 此时将显示“添加域”页，可以在其中输入有关自定义域的信息。 可以选择 Azure 托管的 DNS（建议），也可以选择使用自己的 DNS 提供程序。 如果选择 Azure 托管的 DNS，请选择一个现有的 DNS 区域，然后选择一个自定义子域或创建一个新的子域。 如果使用的是其他 DNS 提供程序，请手动输入自定义域名。 选择“添加”以添加自定义域。

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="添加域页的屏幕截图。":::

    创建新的自定义域时，其验证状态为“正在提交”。

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="域验证状态正在提交的屏幕截图。":::

    等待验证状态更改为“挂起”。 此操作可能需要花费几分钟时间。

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="域验证状态挂起的屏幕截图。":::

1. 选择“挂起”验证状态。 此时将显示一个新页，其中包含验证自定义域所需的 DNS TXT 记录信息。 TXT 记录的形式为 `_dnsauth.<your_subdomain>`。 如果使用的是基于 Azure DNS 的区域，请选择“添加”按钮，将在 Azure DNS 区域中创建一条新的 TXT 记录，该记录拥有所显示的记录值。 如果使用的是其他 DNS 提供程序，请使用页面上所示的记录值手动创建一个名为 `dnsauth.<your_subdomain>` 的新 TXT 记录。

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="验证自定义域页的屏幕截图。":::

1. 选择刷新状态。 使用 DNS TXT 记录对域进行了验证后，验证状态将更改为“已验证”。 此操作可能需要几分钟才能完成验证。

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="自定义域已验证的屏幕截图。":::

1. 关闭页面，以返回到自定义域列表登录页。 自定义域的预配状态应更改为“已预配”，且验证状态应更改为“已批准”。

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="已预配和已批准状态的屏幕截图。":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>将自定义域与 Front Door 终结点相关联

自定义域通过验证后，可将其添加到 Azure Front Door 标准版/高级版终结点。

1. 自定义域通过验证后，可将其关联到现有的 Azure Front Door 标准版/高级版终结点和路由。 选择“终结点关联”链接以打开“关联终结点和路由”页。 选择要与之相关联的终结点和路由。 然后选择“关联”。 关联操作完成后，关闭页面。

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="关联终结点和路由页的屏幕截图。":::

    终结点关联状态应更改为反映自定义域当前关联的终结点。 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="终结点关联链接的屏幕截图。":::

1. 选择 DNS 状态链接。

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="DNS 状态链接的屏幕截图。":::

1. “添加或更新 CNAME 记录”页将出现，其中显示了必须在流量开始流动之前提供的 CNAME 记录信息。 如果使用的是 Azure DNS 托管区域，则可以通过选择页面上的“添加”按钮来创建 CNAME 记录。 如果使用的是其他 DNS 提供程序，则必须手动输入页面上显示的 CNAME 记录名称和值。

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="添加或更新 CNAME 记录的屏幕截图。":::

1. 创建 CNAME 记录并将自定义域关联到 Azure Front Door 终结点完成后，流量将开始流动。

    > [!NOTE]
    > 如果 HTTPS 已启用，则证书预配和传播可能需要花费几分钟时间，因为证书需要传播到所有边缘位置。 

## <a name="verify-the-custom-domain"></a>验证自定义域

完成自定义域的验证和关联后，请确认是否已将自定义域正确引用到终结点。

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="已验证和关联的自定义域的屏幕截图。":::

最后，验证应用程序内容是否是通过浏览器传送。

## <a name="next-steps"></a>后续步骤

若要了解如何为自定义域启用 HTTPS，请继续学习下一教程。

> [!div class="nextstepaction"]
> [为自定义域启用 HTTPS]()
