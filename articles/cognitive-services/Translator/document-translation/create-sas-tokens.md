---
title: 使用 Microsoft 存储资源管理器创建容器和 Blob 的共享访问签名 (SAS) 令牌
description: 如何使用 Microsoft 存储资源管理器与 Azure 门户创建容器和 Blob 的共享访问令牌 (SAS)
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: 8a91829ce79c5cd9e32efefc809dd23a19382f49
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360264"
---
# <a name="create-sas-tokens-for-your-storage-containers"></a>创建存储容器的 SAS 令牌

本文介绍如何使用 Azure 存储资源管理器或 Azure 门户创建共享访问签名令牌 (SAS)。 SAS 令牌提供对 Azure 存储帐户中资源的安全委托式访问。

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>使用 Azure 存储资源管理器创建 SAS 令牌

### <a name="prerequisites"></a>先决条件

* 需在 Windows、macOS 或 Linux 开发环境中安装一个 [**Azure 存储资源管理器**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md)应用。 Azure 存储资源管理器是一个免费的工具，可让你轻松管理 Azure 云存储资源。
* 安装 Azure 存储资源管理器应用后，请[将其连接到用于文档翻译的存储帐户](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service)。

### <a name="create-your-tokens"></a>创建令牌

### <a name="sas-tokens-for-containers"></a>[容器的 SAS 令牌](#tab/Containers)

1. 在本地计算机上打开 Azure 存储资源管理器应用，并导航到连接的 **存储帐户**。
1. 展开“存储帐户”节点，并选择“Blob 容器”。
1. 展开“Blob 容器”节点，然后右键单击某个存储 **容器** 节点以显示选项菜单。
1. 从选项菜单中选择“获取共享访问签名...”。
1. 在“共享访问签名”窗口中做出以下选择：
    * 选择“访问策略”（默认值为“无”）。
    * 指定已签名密钥的“开始”与“过期”日期和时间。  建议指定较短的时间范围，因为 SAS 一旦生成，便不可撤销。
    * 选择“开始”与“过期”日期和时间的时区（默认值为“本地”）。
    * 通过选中和/或清除相应的复选框来定义容器“权限”。
    * 复查设置并选择“创建”。

1. 此时会显示一个新窗口，其中包含容器的“容器名称”、“URI”和“查询字符串”。    
1. **请将容器、URI 和查询字符串值复制并粘贴到安全的位置。这些信息只会显示一次，在关闭窗口后不再可以检索到。**
1. 若要构造 SAS URL，请将 SAS 令牌 (URI) 追加到存储服务的 URL。

### <a name="sas-tokens-for-blobs"></a>[Blob 的 SAS 令牌](#tab/blobs)

1. 在本地计算机上打开 Azure 存储资源管理器应用，并导航到连接的 **存储帐户**。
1. 展开存储节点并选择“Blob 容器”。
1. 展开“Blob 容器”节点，并选择某个 **容器** 节点以在主窗口中显示内容。
1. 选择要在其中委托 SAS 访问权限的 Blob，然后右键单击以显示选项菜单。
1. 从选项菜单中选择“获取共享访问签名...”。
1. 在“共享访问签名”窗口中做出以下选择：
    * 选择“访问策略”（默认值为“无”）。
    * 指定已签名密钥的“开始”与“过期”日期和时间。  建议指定较短的时间范围，因为 SAS 一旦生成，便不可撤销。
    * 选择“开始”与“过期”日期和时间的时区（默认值为“本地”）。
    * 通过选中和/或清除相应的复选框来定义容器“权限”。
    * 复查设置并选择“创建”。
1. 此时会显示一个新窗口，其中包含 Blob 的“Blob 名称”、“URI”和“查询字符串”。    
1. **请将 Blob、URI 和查询字符串值复制并粘贴到安全的位置。这些信息只会显示一次，在关闭窗口后不再可以检索到。**
1. 若要构造 SAS URL，请将 SAS 令牌 (URI) 追加到存储服务的 URL。

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>在 Azure 门户中创建 Blob 的 SAS 令牌

> [!NOTE]
> 目前不支持直接在 Azure 门户中创建容器的 SAS 令牌。 但是，可以使用 [**Azure 存储资源管理器**](#create-your-sas-tokens-with-azure-storage-explorer)创建 SAS 令牌，或者 [以编程方式](../../../storage/blobs/sas-service-create.md)完成该任务。

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>先决条件

若要开始，需要：

* 一个有效的 [**Azure 帐户**](https://azure.microsoft.com/free/cognitive-services/)。  如果没有帐户，可以 [**创建一个免费帐户**](https://azure.microsoft.com/free/)。
* 一个 [**翻译器**](https://ms.portal.azure.com/#create/Microsoft)服务资源（**并非** 认知服务多服务资源）。  请参阅[创建新的 Azure 资源](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource)。  
* 一个 [Azure Blob 存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 你将创建一个容器，以便存储和组织存储帐户中的 Blob 数据。

### <a name="create-your-tokens"></a>创建令牌

转到 [Azure 门户](https://ms.portal.azure.com/#home)并按如下所述在其中导航：  

 **你的存储帐户** → **容器** → **你的容器** → **你的 Blob**

1. 在靠近页面顶部的菜单中选择“生成 SAS”。

1. 选择“签名方法”→“用户委托密钥”。 

1. 通过选中和/或清除相应的复选框来定义“权限”。

1. 指定已签名密钥的“开始”与“过期”时间。 

1. “允许的 IP 地址”字段是可选的，它指定要从中接受请求的 IP 地址或 IP 地址范围。 如果请求 IP 地址与 SAS 令牌中指定的 IP 地址或地址范围不匹配，则不会为此请求 IP 地址授权。

1. “允许的协议”字段是可选的，它指定通过 SAS 发出的请求所允许的协议。 默认值为 HTTPS。

1. 复查设置，然后选择“生成 SAS 令牌和 URL”。

1. “Blob SAS 令牌”查询字符串和“Blob SAS URL”将显示在窗口的下部区域。   

1. **请将 Blob SAS 令牌和 URL 值复制并粘贴到安全的位置。这些信息只会显示一次，在关闭窗口后不再可以检索到。**

1. 若要构造 SAS URL，请将 SAS 令牌 (URI) 追加到存储服务的 URL。

## <a name="learn-more"></a>了解更多

* [以编程方式创建 Blob 或容器的 SAS 令牌](../../../storage/blobs/sas-service-create.md)
* [目录、容器或 Blob 的权限](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [文档翻译入门](get-started-with-document-translation.md)
>
>
