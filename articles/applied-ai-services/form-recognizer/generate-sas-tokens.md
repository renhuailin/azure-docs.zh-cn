---
title: 使用 Azure 门户为容器和 Blob 生成共享访问签名 (SAS) 令牌。
description: 如何在 Azure 门户中为容器和 Blob 生成共享访问令牌 (SAS)
ms.topic: how-to
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.date: 09/23/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 6e1b145c5efa9135a198cd6623c450f5965a6c2c
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387936"
---
# <a name="generate-sas-tokens-for-storage-containers"></a>为存储容器生成 SAS 令牌

 在本文中，你将了解如何为 Azure Blob 存储容器生成用户委派共享访问签名 (SAS) 令牌。 用户委派 SAS 令牌使用 Azure Active Directory (Azure AD) 凭据而不是 Azure 存储密钥进行签名。 它提供对 Azure 存储帐户中资源的高级安全和委派访问。
从较高层次来看，这是它的工作原理：应用程序将 SAS 令牌作为请求的一部分提供给 Azure 存储。 如果存储服务验证 SAS 有效，则对请求进行授权。 如果 SAS 被视为无效，则请求被拒绝，错误代码为 403（已禁止）。

Azure Blob 存储提供了三种类型的资源：

* 存储帐户在 Azure 中为数据提供唯一的命名空间。
* 容器位于存储帐户中，并组织 Blob 集。
* Blob 位于容器中，并存储文本和二进制数据。

> [!NOTE]
>
> * 如果 Azure 存储帐户受虚拟网络 (VNet) 或防火墙保护，则无法使用 SAS 令牌授予访问权限。 必须使用[托管标识](managed-identity-byos.md)授予对存储资源的访问权限。
>
> * [托管标识](managed-identity-byos.md)支持专用和可公开访问的 Azure Blob 存储帐户。
>

## <a name="when-to-use-a-shared-access-signature"></a>何时使用共享访问签名

* 如果使用的是具有公共访问权限的存储容器，则可以选择使用 SAS 令牌授予对存储资源的有限访问权限。

* 训练自定义模型时，必须将已组装的训练文档集上传到 Azure Blob 存储容器。 可使用用户委派 SAS 令牌授予对训练资源的权限。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

* 一个有效的 [**Azure 帐户**](https://azure.microsoft.com/free/cognitive-services/)。  如果没有帐户，可以 [**创建一个免费帐户**](https://azure.microsoft.com/free/)。
* [表单识别器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)或[认知服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)多服务资源。
* 标准性能 [Azure Blob 存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 你需创建一个容器，用于存储和整理存储帐户中的 blob 数据。 如果不知道如何使用容器创建 Azure 存储帐户，请按照以下快速入门中的说明操作：

  * [创建存储帐户](/azure/storage/common/storage-account-create)。 创建存储帐户时，请确保在“实例详细信息”→“性能”字段中选择“标准”性能 。
  * [创建容器](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)。 创建容器时，在“新建容器”窗口中将“公共访问级别”字段设置为“容器（对容器和 Blob 进行匿名读取访问）”  。

## <a name="upload-your-documents"></a>上传文档

1. 转到 [Azure 门户](https://ms.portal.azure.com/#home)并按如下方式导航：存储帐户→数据存储→容器  

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="屏幕截图：Azure 门户中的数据存储菜单。":::

1. 从列表中选择容器。
1. 从页面顶部的菜单中选择“上传”。

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="屏幕截图：Azure 门户中的容器上传按钮。":::

1. 将显示“上传 Blob”窗口。
1. 选择要上传的文件。

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="屏幕截图：Azure 门户中的上传 Blob 窗口。":::

> [!NOTE]
> 默认情况下，REST API 将使用位于存储容器根目录下的表单文档。 但是，如果在 API 调用中指定，则可使用在子文件夹中组织的数据。 请参阅[在子文件夹中组织数据](/azure/applied-ai-services/form-recognizer/build-training-data-set#organize-your-data-in-subfolders-optional)

## <a name="create-a-sas-with-the-azure-portal"></a>使用 Azure 门户创建 SAS

> [!IMPORTANT]
>
> 请为容器生成和检索 SAS，而不是为存储帐户本身。

1. 在 [Azure 门户](https://ms.portal.azure.com/#home)中，按如下方式导航：

     存储帐户→容器 
1. 从列表中选择容器。
1. 导航到主窗口的右侧，并选择与所选容器关联的三个椭圆。
1. 从下拉菜单中选择“生成 SAS”以打开“生成 SAS”窗口 。

    :::image type="content" source="media/sas-tokens/generate-sas.png" alt-text="屏幕截图（Azure 门户）：生成 SAS 令牌下拉菜单。":::

1. 选择“签名方法”→“用户委托密钥”。 

1. 通过选中或清除相应的复选框来定义“权限”。 请确保选中“读取”、“写入”、“删除”和“列出”权限   。

    :::image type="content" source="media/sas-tokens/sas-permissions.png" alt-text="屏幕截图（Azure 门户）：SAS 权限字段。":::

    >[!IMPORTANT]
    >
    > * 如果收到类似于下面的消息，则需要在存储帐户中分配对 Blob 数据的访问权限：
    >
    >     :::image type="content" source="media/sas-tokens/need-permissions.png" alt-text="屏幕截图：缺少权限警告。":::
    >
     > * [Azure 基于角色的访问控制](/azure/role-based-access-control/overview) (Azure RBAC) 是用于管理对 Azure 资源的访问权限的授权系统。 Azure RBAC 可帮助管理 Azure 资源的访问权限和权限。
    > * 遵循我们的[分配 Azure 角色以访问 Blob 数据](/azure/role-based-access-control/role-assignments-portal?tabs=current)指南来分配角色，以允许 Azure 存储容器（如[存储 Blob 数据参与者](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)）的读取、写入和删除权限 。

1. 指定已签名密钥的“开始”与“过期”时间。  到期时间的值最多为从 SAS 开始的七天。

1. “允许的 IP 地址”字段是可选的，它指定要从中接受请求的 IP 地址或 IP 地址范围。 如果请求 IP 地址与 SAS 令牌中指定的 IP 地址或地址范围不匹配，则不会为此请求 IP 地址授权。

1. “允许的协议”字段是可选的，它指定通过 SAS 发出的请求所允许的协议。 默认值为 HTTPS。

1. 复查设置，然后选择“生成 SAS 令牌和 URL”。

1. “Blob SAS 令牌”查询字符串和“Blob SAS URL”将显示在窗口的下部区域。  要使用 Blob SAS 令牌，请为其追加存储服务 URI。

1. **请将 Blob SAS 令牌和 URL 值复制并粘贴到安全的位置。这些信息只会显示一次，在关闭窗口后不再可以检索到。**

## <a name="create-a-sas-with-azure-command-line-interface-cli"></a>使用 Azure 命令行接口 (CLI) 创建 SAS

1. 要使用 Azure CLI 为容器创建用户委派 SAS，请确保已安装 2.0.78 或更高版本。 若要检查已安装的版本，请使用 `az --version` 命令。

1. 调用 [az storage container generate-sas](/cli/azure/storage/container?view=azure-cli-latest&preserve-view=true#az_storage_container_generate_sas) 命令。

1. 下列参数必填：

    * `auth-mode login`. 此参数确保使用 Azure AD 凭据为向 Azure 存储发出的请求授权。
    * `as-user`.  此参数指示生成的 SAS 是用户委派 SAS。

1. 容器上的用户委派 SAS 支持的权限包括添加 (a)、创建 (c)、删除 (d)、列出 (l)、读取 (r) 和写入 (w)。  请确保将 r、w、d 和 l 包含为权限参数的一部分   。

1. 使用 Azure CLI 创建用户委派 SAS 时，用户委派密钥有效的最大时间间隔为从开始日期起的七天。 因此，应该为 SAS 指定在开始时间七天内的到期时间。 请参阅[使用 Azure CLI 为容器或 Blob 创建用户委派 SAS](/azure/storage/blobs/storage-blob-user-delegation-sas-create-cli#use-azure-ad-credentials-to-secure-a-sas)

### <a name="example"></a>示例

生成用户委派 SAS。  请将括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions rwdl \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

## <a name="how-to-use-your-blob-sas-url"></a>如何使用 Blob SAS URL

* 要将 Blob SAS URL 与 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) 一起使用，请将 SAS URL 添加到请求正文：

  ```json
  {
      "source":"<BLOB SAS URL>"
  }
  ```

* 要将 Blob SAS URL 与[表单识别器标记工具](https://fott-2-1.azurewebsites.net/connections/create)一起使用，请将 SAS URL 添加到“连接设置”→“Azure Blob 容器”→“SAS URI”字段    ：

  :::image type="content" source="media/sas-tokens/fott-add-sas-uri.png" alt-text="{alt-text}":::

就这么简单。 已了解如何生成 SAS 令牌来授权客户端访问数据的方式。

> [!div class="nextstepaction"]
> [生成训练数据集](build-training-data-set.md)
