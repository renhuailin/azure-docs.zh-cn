---
title: Azure 存储中的静态网站托管
description: Azure 存储静态网站托管提供经济高效、可缩放的解决方案用于托管新式 Web 应用程序。
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 09/04/2020
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: 388b84dc831984cf03b91ff16e4cfb8962b88c73
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662716"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure 存储中的静态网站托管

可以直接通过名为 *$web* 的存储容器提供静态内容（HTML、CSS、JavaScript 和图像文件）。 通过将内容托管在 Azure 存储中，你可以使用无服务器体系结构，包括 [Azure Functions](../../azure-functions/functions-overview.md) 和其他平台即服务 (PaaS) 服务。 如果你不需要 Web 服务器来呈现内容，Azure 存储静态网站托管是一个不错的选择。

[应用服务静态 Web 应用](https://azure.microsoft.com/services/app-service/static/)是 Azure 存储静态网站托管的绝佳替代方案，也适用于不需要 Web 服务器呈现内容的案例。 应用服务静态 Web 应用提供了从 GitHub 源到全局部署的完全托管的持续集成和持续交付 (CI/CD) 工作流。

如果需要 Web 服务器来呈现内容，可以使用 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)。

## <a name="setting-up-a-static-website"></a>设置静态网站

静态网站托管是必须在存储帐户上启用的一项功能。

若要启用静态网站托管，请选择默认文件的名称，然后可以提供自定义 404 页面的路径。 如果帐户中尚不存在名为 **$web** 的 blob 存储容器，则会为你创建一个。 将站点的文件添加到此容器。

有关分步指南，请参阅[在 Azure 存储中托管静态网站](storage-blob-static-website-how-to.md)。

![Azure 存储静态网站指标 - 指标](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$web** 容器中的文件区分大小写，通过匿名访问请求来提供，只能在读取操作中使用。

## <a name="uploading-content"></a>上传内容

可以使用下列工具中的任何一种将内容上传到 **$web** 容器：

> [!div class="checklist"]
> - [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> - [Azure PowerShell 模块](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> - [AzCopy](../common/storage-use-azcopy-v10.md)
> - [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)
> - [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> - [Visual Studio Code 扩展](- https://channel9.msdn.com/Shows/Docs-Azure/Deploy-static-website-to-Azure-from-Visual-Studio-Code/player)

## <a name="viewing-content"></a>查看内容

用户可以在浏览器中使用网站的公共 URL 来查看站点内容。 可以使用 Azure 门户、Azure CLI 或 PowerShell 查找 URL。 请参阅[查找网站 URL](storage-blob-static-website-how-to.md#portal-find-url)。

当用户打开站点并且未指定特定文件（例如 `https://contosoblobaccount.z22.web.core.windows.net`）时，将出现在启用静态网站托管时指定的索引文档。

如果服务器返回 404 错误，并且你在启用网站时未指定错误文档，则会向用户返回默认 404 页面。

> [!NOTE]
> 静态网站不支持[对 Azure 存储的跨域资源共享 (CORS) 支持](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)。

### <a name="secondary-endpoints"></a>辅助终结点

如果[在次要区域中设置冗余](../common/storage-redundancy.md#redundancy-in-a-secondary-region)，则还可以使用辅助终结点访问网站内容。 由于数据以异步方式复制到次要区域，因此辅助终结点上可用的文件并不总是与主终结点上可用的文件同步。

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>设置 Web 容器的公共访问级别的影响

你可以修改 **$web** 容器的公共访问级别，但这不会影响主静态网站终结点，因为这些文件是通过匿名访问请求来提供的。 这意味着对所有文件的公共（只读）访问权限。

以下屏幕截图显示了 Azure 门户中的公共访问级别设置：

![显示如何在门户中设置公共访问级别的屏幕截图](./media/anonymous-read-access-configure/configure-public-access-container.png)

尽管主静态网站终结点不受影响，但更改公共访问级别会影响主 blob 服务终结点。

例如，如果将 **$web** 容器的公共访问级别从 **专用（不允许匿名访问）** 更改为 **Blob（仅允许匿名读取 blob）** ，则对主静态网站终结点 `https://contosoblobaccount.z22.web.core.windows.net/index.html` 的公共访问级别不会更改。

但是，对主 blob 服务终结点 `https://contosoblobaccount.blob.core.windows.net/$web/index.html` 的公共访问权限将从专用更改为公共。 现在，用户可以使用这两个终结点中的任意一个来打开该文件。

禁止对存储帐户的公共访问不会影响该存储帐户中承载的静态网站。 有关详细信息，请参阅[配置对容器和 Blob 的匿名公共读取访问](anonymous-read-access-configure.md)。

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>将自定义域映射到静态网站 URL

你可以使静态网站可通过自定义域进行访问。

为自定义域启用 HTTP 访问会更加容易，因为 Azure 存储本机支持该访问。 若要启用 HTTPS，必须使用 Azure CDN，因为 Azure 存储尚不提供对“自定义域使用 HTTPS”的本机支持。 有关分步指南，请参阅[将自定义域映射到 Azure Blob 存储终结点](storage-custom-domain-name.md)。

如果存储帐户配置为[需要通过 HTTPS 进行安全传输](../common/storage-require-secure-transfer.md)，则用户必须使用 HTTPS 终结点。

> [!TIP]
> 考虑在 Azure 上托管域。 有关详细信息，请参阅[在 Azure DNS 中托管域](../../dns/dns-delegate-domain-azure-dns.md)。

## <a name="adding-http-headers"></a>添加 HTTP 标头

无法将标头配置为静态网站功能的一部分。 但是，可以使用 Azure CDN 来添加标头和追加（或覆盖）标头值。 请参阅 [Azure CDN 的标准规则引擎参考](../../cdn/cdn-standard-rules-engine-reference.md)。

如果要使用标头来控制缓存，请参阅[使用缓存规则控制 Azure CDN 缓存行为](../../cdn/cdn-caching-rules.md)。

## <a name="multi-region-website-hosting"></a>多区域网站托管

如果你计划在多个地理位置托管一个网站，建议你使用[内容分发网络](../../cdn/index.yml)进行区域缓存。 如果要在每个区域提供不同的内容，请使用 [Azure Front Door](../../frontdoor/index.yml)。 此外，它还提供故障转移功能。 如果计划使用自定义域，则不建议使用 [Azure 流量管理器](../../traffic-manager/index.yml)。 考虑到 Azure 存储验证自定义域名的方式，可能会出现问题。

## <a name="permissions"></a>权限

能够启用静态网站的权限是 Microsoft.Storage/storageAccounts/blobServices/write 或共享式密钥。  提供此访问权限的内置角色包括存储帐户参与者。

## <a name="pricing"></a>定价

可以免费启用静态网站托管。 只会针对你的站点利用的 blob 存储和运营成本进行计费。 如需详细了解 Azure Blob 存储价格，请参阅 [Azure Blob 存储定价页](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="metrics"></a>指标

可以在静态网站页面上启用指标。 启用指标后，指标仪表板会报告有关 $web 容器中的文件的流量统计信息。

若要在静态网站页面上启用指标，请参阅[在静态网站页面上启用指标](storage-blob-static-website-how-to.md#metrics)。

## <a name="feature-support"></a>功能支持

此表显示了你的帐户如何支持此功能，以及启用某些功能时对支持的影响。

| 存储帐户类型                | Blob 存储（默认支持）   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 标准常规用途 v2 | ![是](../media/icons/yes-icon.png) |![是](../media/icons/yes-icon.png)              | ![是](../media/icons/yes-icon.png) |
| 高级块 blob          | ![是](../media/icons/yes-icon.png)|![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

## <a name="faq"></a>常见问题解答

##### <a name="does-the-azure-storage-firewall-work-with-a-static-website"></a>Azure 存储防火墙是否适用于静态网站？

是的。 存储帐户[网络安全规则](../common/storage-network-security.md)（包括基于 IP 的和 VNET 防火墙）支持静态网站终结点，并且可用于保护网站。

##### <a name="do-static-websites-support-azure-active-directory-azure-ad"></a>静态网站是否支持 Azure Active Directory (Azure AD)？

不是。 静态网站仅支持对 $web 容器中文件的匿名公共读取访问。

##### <a name="how-do-i-use-a-custom-domain-with-a-static-website"></a>如何将自定义域与静态网站配合使用？

可以使用 [Azure 内容分发网络 (Azure CDN)](./storage-custom-domain-name.md#map-a-custom-domain-with-https-enabled) 在静态网站中配置[自定义域](./static-website-content-delivery-network.md)。 Azure CDN 在世界各地为网站提供了一致的低延迟。

##### <a name="how-do-i-use-a-custom-ssl-certificate-with-a-static-website"></a>如何将自定义 SSL 证书用于静态网站？

可以使用 [Azure CDN](./storage-custom-domain-name.md#map-a-custom-domain-with-https-enabled) 在静态网站中配置[自定义 SSL](./static-website-content-delivery-network.md) 证书。 Azure CDN 在世界各地为网站提供了一致的低延迟。

##### <a name="how-do-i-add-custom-headers-and-rules-with-a-static-website"></a>如何向静态网站添加自定义标头和规则？

可以使用 [Azure CDN - Verizon 高级](../../cdn/cdn-verizon-premium-rules-engine.md)为静态网站配置主机标头。 我们非常期待你能通过此处提供反馈。

##### <a name="why-am-i-getting-an-http-404-error-from-a-static-website"></a>为什么会从静态网站收到 HTTP 404 错误？

如果使用错误的大小写引用文件名，则会发生这种情况。 例如，`Index.html` 而非 `index.html`。 静态网站的 URL 中的文件名和扩展名区分大小写，即使通过 HTTP 提供也是如此。 如果尚未预配 Azure CDN 终结点，也会发生这种情况。 预配新的 CDN 后，最长等待 90 分钟，以完成传播。

##### <a name="why-isnt-the-root-directory-of-the-website-not-redirecting-to-the-default-index-page"></a>为什么网站的根目录不重定向到默认索引页？

在 Azure 门户中，打开帐户的静态网站配置页，并查找在“索引文档名称”字段中设置的名称和扩展名。 确保此名称与位于存储帐户的 $web 容器中的文件的名称完全相同。 静态网站的 URL 中的文件名和扩展名区分大小写，即使通过 HTTP 提供也是如此。

## <a name="next-steps"></a>后续步骤

- [在 Azure 存储中托管静态网站](storage-blob-static-website-how-to.md)
- [将自定义域映射到 Azure Blob 存储终结点](storage-custom-domain-name.md)
- [Azure Functions](../../azure-functions/functions-overview.md)
- [Azure 应用服务](../../app-service/overview.md)
- [生成首个无服务器 Web 应用程序](/azure/functions/tutorial-static-website-serverless-api-with-database)
- [教程：在 Azure DNS 中托管域](../../dns/dns-delegate-domain-azure-dns.md)
