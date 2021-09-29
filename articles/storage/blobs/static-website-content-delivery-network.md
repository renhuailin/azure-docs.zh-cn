---
title: 将静态网站与 Azure CDN 集成 - Azure 存储
description: 了解如何使用 Azure 内容分发网络 (CDN) 缓存 Azure 存储帐户中的静态网站内容。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: fcc79c6b1828d4feb33a73f04e98f92abbf7384e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597011"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>将静态网站与 Azure CDN 集成

可以启用 [Azure 内容分发网络 (CDN)](../../cdn/cdn-overview.md) 来缓存 Azure 存储帐户中托管的[静态网站](storage-blob-static-website.md)上的内容。 可以使用 Azure CDN 配置静态网站的自定义域终结点，预配自定义 TLS/SSL 证书，以及配置自定义重写规则。 配置 Azure CDN 需额外付费，但用户从世界上的任何位置访问你的网站时，其延迟会始终很低。 Azure CDN 还提供使用你自己的证书进行 TLS 加密的功能。

有关 Azure CDN 定价的详细信息，请参阅 [Azure CDN 定价](https://azure.microsoft.com/pricing/details/cdn/)。

## <a name="enable-azure-cdn-for-your-static-website"></a>为静态网站启用 Azure CDN

可以直接从存储帐户中为静态网站启用 Azure CDN。 若要为 CDN 终结点指定高级配置设置（例如[大文件下载优化](../../cdn/cdn-optimization-overview.md#large-file-download)），可以改用 [Azure CDN 扩展](../../cdn/cdn-create-new-endpoint.md)来创建 CDN 配置文件和终结点。

1. 在 Azure 门户中找到存储帐户并显示帐户概览。

1. 在“安全性 + 网络”菜单下，选择“Azure CDN”以打开“Azure CDN”页  ：

    ![创建 CDN 终结点](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. 在“CDN 配置文件”部分，指定新建 CDN 配置文件还是使用现有的 CDN 配置文件。 CDN 配置文件是共享定价层和提供商的 CDN 终结点的集合。 输入 CDN 的名称，该名称在订阅中是唯一的。

1. 指定 CDN 终结点的定价层。 有关定价的更多信息，请参阅[内容分发网络定价](https://azure.microsoft.com/pricing/details/cdn/)。 有关每个层中可用功能的更多信息，请参阅[比较 Azure CDN 产品功能](../../cdn/cdn-features.md)。

1. 在“CDN 终结点名称”字段中，指定 CDN 终结点的名称。 CDN 终结点必须在 Azure 中是唯一的，并提供终结点 URL 的第一个组成部分。 窗体验证终结点名称是否唯一。

1. 在“源主机名”字段中指定静态网站终结点。

   若要查找静态网站终结点，请导航到存储帐户的“静态网站”设置。  复制主终结点并将其粘贴到 CDN 配置中。

   > [!IMPORTANT]
   > 确保删除 URL 中的协议标识符（例如 HTTPS）和尾斜杠。 例如，如果静态网站终结点为 `https://mystorageaccount.z5.web.core.windows.net/`，则应在“源主机名”字段中指定 `mystorageaccount.z5.web.core.windows.net`。

   下图显示了一个终结点配置示例：

   ![显示 CDN 终结点配置示例的屏幕截图](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. 选择“创建”，然后等待 CDN 预配完毕。 创建终结点之后，它会出现在终结点列表中。 （如果窗体中有任何错误，则该字段旁将显示感叹号。）

1. 若要验证 CDN 终结点是否已正确配置，请单击该终结点，导航到其设置。 在存储帐户的 CDN 概览中找到终结点主机名，导航到终结点，如下图所示。 CDN 终结点的格式将类似于 `https://staticwebsitesamples.azureedge.net`。

    ![显示 CDN 终结点概览的屏幕截图](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. 预配 CDN 终结点后，导航到 CDN 终结点就会显示以前上传到静态网站的 index.html 文件的内容。

1. 若要查看 CDN 终结点的源设置，请导航到 CDN 终结点的“设置”部分下的“源”。 此时会看到“源类型”字段设置为“自定义源”，“源主机名”字段显示静态网站终结点。

    ![显示 CDN 终结点的源设置的屏幕截图](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>删除 CDN 中的内容

如果不再需要在 Azure CDN 中缓存对象，则可以执行以下步骤之一：

- 将容器设为专用容器而不是公用容器。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](./anonymous-read-access-configure.md)。
- 通过 Azure 门户禁用或删除 CDN 终结点。
- 将托管服务修改为不再响应此对象的请求。

已在 Azure CDN 中缓存的对象保持缓存状态，直至对象的生存时间到期，或直至[清除](../../cdn/cdn-purge-endpoint.md)终结点为止。 当生存时间到期时，Azure CDN 会确定 CDN 终结点是否仍有效，以及是否仍可对该对象进行匿名访问。 如果不能，则不再缓存该对象。

## <a name="next-steps"></a>后续步骤

（可选）将自定义域添加到 Azure CDN 终结点。 请参阅[教程：将自定义域添加到 Azure CDN 终结点](../../cdn/cdn-map-content-to-custom-domain.md)。
