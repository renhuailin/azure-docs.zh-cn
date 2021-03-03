---
title: 使用 Microsoft 存储资源管理器为容器和 blob 创建共享访问签名 (SAS) 令牌
description: 如何使用 Microsoft 存储资源管理器和 Azure 门户为容器和 blob (SAS) 创建共享访问令牌
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 104329928c507c288537704fbbef4e35b65cb445
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739430"
---
# <a name="create-sas-tokens-for-document-translation"></a>创建用于文档翻译的 SAS 令牌

本文介绍如何使用 Azure 存储资源管理器或 Azure 门户 (SAS) 令牌创建共享访问签名。 SAS 令牌提供对 Azure 存储帐户中的资源进行安全的委托访问。

## <a name="create-sas-tokens-with-azure-storage-explorer"></a>创建带 Azure 存储资源管理器的 SAS 令牌

### <a name="prerequisites"></a>先决条件

* 你需要在 Windows、macOS 或 Linux 开发环境中安装 [**Azure 存储资源管理器**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) 应用。 Azure 存储资源管理器是一种免费工具，可让你轻松管理 Azure 云存储资源。
* 安装 Azure 存储资源管理器应用后， [将其连接到用于文档转换的存储帐户](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) 。

### <a name="create-your-tokens"></a>创建令牌

### <a name="sas-tokens-for-containers"></a>[容器的 SAS 令牌](#tab/Containers)

1. 在本地计算机上打开 Azure 存储资源管理器应用，并导航到已连接的 **存储帐户**。
1. 展开 "存储帐户" 节点，并选择 " **Blob 容器**"。
1. 展开 "Blob 容器" 节点，右键单击存储 **容器** 节点，或显示 "选项" 菜单。
1. 从 "选项" 菜单中选择 " **获取共享访问签名 ...** "。
1. 在 " **共享访问签名** " 窗口中，进行以下选择：
    * 选择 **访问策略** (默认值为 "无") 。
    * 指定签名密钥的 **开始** 日期和 **到期** 日期和时间。 建议使用较短的生存期，因为在生成后，不能撤销 SAS。
    * 为开始日期和到期日期和时间 **选择时区，** (默认为本地) 。
    * 通过选中或清除相应的复选框来定义容器 **权限** 。
    * 查看并选择 " **创建**"。

1. 将显示一个新窗口，其中包含容器的 **容器** 名称、 **URI** 和 **查询字符串** 。  
1. **将容器、URI 和查询字符串值复制并粘贴到一个安全的位置。它们只会出现一次，并且在窗口关闭后无法检索。**
1. 若要构造 SAS URL，请在存储服务的 URL)  (URI 添加 SAS 令牌。

### <a name="sas-tokens-for-blobs"></a>[Blob 的 SAS 令牌](#tab/blobs)

1. 在本地计算机上打开 Azure 存储资源管理器应用，并导航到已连接的 **存储帐户**。
1. 展开 "存储" 节点，并选择 " **Blob 容器**"。
1. 展开 "Blob 容器" 节点，然后选择 **容器** 节点以在主窗口中显示内容。
1. 选择要在其中委托 SAS 访问的 blob，然后右键单击以显示 "选项" 菜单。
1. 从 "选项" 菜单中选择 " **获取共享访问签名 ...** "。
1. 在 " **共享访问签名** " 窗口中，进行以下选择：
    * 选择 **访问策略** (默认值为 "无") 。
    * 指定签名密钥的 **开始** 日期和 **到期** 日期和时间。 建议使用较短的生存期，因为在生成后，不能撤销 SAS。
    * 为开始日期和到期日期和时间 **选择时区，** (默认为本地) 。
    * 通过选中或清除相应的复选框来定义容器 **权限** 。
    * 查看并选择 " **创建**"。
1. 将显示一个新窗口，其中包含 blob 的 **blob** 名称、 **URI** 和 **查询字符串** 。  
1. **将 blob、URI 和查询字符串值复制并粘贴到安全位置。它们只会出现一次，并且在窗口关闭后无法检索。**
1. 若要构造 SAS URL，请在存储服务的 URL)  (URI 添加 SAS 令牌。

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>为 Azure 门户中的 blob 创建 SAS 令牌

> [!NOTE]
> 当前不支持在 Azure 门户中直接为容器创建 SAS 令牌。 但是，可以使用 [**Azure 存储资源管理器**](#create-sas-tokens-with-azure-storage-explorer) 创建 SAS 令牌，也可以 [通过编程方式](../../../storage/blobs/sas-service-create.md)完成任务。

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>先决条件

若要开始，你将需要：

* 有效的 [**Azure 帐户**](https://azure.microsoft.com/free/cognitive-services/)。  如果没有帐户，可以 [**创建一个免费帐户**](https://azure.microsoft.com/free/)。
* [**翻译员**](https://ms.portal.azure.com/#create/Microsoft)服务资源 (**不** 是认知服务多服务资源。  *请参阅*[创建新的 Azure 资源](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource)。  
* 一个 [**Azure blob 存储帐户**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 对 Azure 存储进行的所有访问都要通过存储帐户完成。

### <a name="create-your-tokens"></a>创建令牌

转到 [Azure 门户](https://ms.portal.azure.com/#home) ，并按如下所示导航：  

 **存储帐户** **→容器** → **容器** → **你的 blob**

1. 从页面顶部附近的菜单中选择 " **生成 SAS** "。

1. 选择 " **签名方法** → **用户委托密钥**"。

1. 通过选中或清除相应的复选框来定义 **权限** 。

1. 指定已签名的密钥 **开始** 和 **到期** 时间。

1. " **允许的 ip 地址** " 字段是可选的，它指定要从中接受请求的 ip 地址或 ip 地址范围。 如果请求 IP 地址与 SAS 令牌中指定的 IP 地址或地址范围不匹配，则不会对其进行授权。

1. **允许的协议** 字段是可选的，它指定了使用 SAS 发出请求时允许使用的协议。 默认值为 HTTPS。

1. 查看并选择 " **生成 SAS 令牌和 URL**"。

1. **BLOB sas 令牌** 查询字符串和 **blob sas URL** 将显示在窗口的下部区域中。  

1. **将 Blob SAS 令牌和 URL 值复制并粘贴到安全位置。它们只会出现一次，并且在窗口关闭后无法检索。**

1. 若要构造 SAS URL，请在存储服务的 URL)  (URI 添加 SAS 令牌。

## <a name="learn-more"></a>了解详细信息

* [以编程方式为 blob 或容器创建 SAS 令牌](../../../storage/blobs/sas-service-create.md)
* [目录、容器或 blob 的权限](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [文档翻译入门](get-started-with-document-translation.md)
>
>