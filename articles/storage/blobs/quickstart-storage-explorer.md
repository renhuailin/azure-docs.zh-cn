---
title: 快速入门 - 使用 Azure 存储资源管理器创建 blob
titleSuffix: Azure Storage
description: 了解如何使用 Azure 存储资源管理器创建容器和 blob、将 blob 下载到本地计算机，以及查看容器中的所有 blob。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: tamram
ms.openlocfilehash: abf75bb39610deb54b72002cde3dd8e28131ed94
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838892"
---
# <a name="quickstart-use-azure-storage-explorer-to-create-a-blob"></a>快速入门：使用 Azure 存储资源管理器创建 blob

本快速入门介绍如何使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)创建容器和 blob。 接下来，将了解如何将 blob 下载到本地计算机，以及如何在容器中查看所有 blob。 此外，还将了解如何创建 blob 快照、管理容器访问策略以及创建共享访问签名。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

本快速入门要求安装 Azure 存储资源管理器。 若要安装适用于 Windows、Macintosh 或 Linux 的 Azure 存储资源管理器，请参阅 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="log-in-to-storage-explorer"></a>登录到存储资源管理器

首次启动时，会显示“Microsoft Azure 存储资源管理器 - 连接”窗口。 存储资源管理器提供了多种连接到存储帐户的方式。 下表列出了不同的连接方式：

|任务|目的|
|---|---|
|添加 Azure 帐户 | 将你重定向到组织的登录页，向 Azure 进行身份验证。 |
|使用连接字符串或共享访问签名 URI | 可用于通过 SAS 令牌或共享连接字符串直接访问容器或存储帐户。 |
|使用存储帐户名称和密钥| 使用存储帐户的存储帐户名称和密钥连接到 Azure 存储。|

选择“添加 Azure 帐户”  ，并单击“登录”  。遵照屏幕提示登录到 Azure 帐户。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-connect.png" alt-text="显示“Microsoft Azure 存储资源管理器 - 连接”窗口的屏幕截图":::

存储资源管理器完成连接后，便会显示“资源管理器”选项卡。以下视图可以查看通过 [Azure 存储模拟器](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)、[Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 帐户或 [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 环境配置的所有 Azure 存储帐户和本地存储。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-main-page.png" alt-text="显示存储资源管理器主页面的屏幕截图":::

## <a name="create-a-container"></a>创建容器

若要创建容器，请展开在前面的步骤中创建的存储帐户。 选择“Blob 容器”，然后右键单击并选择“创建 Blob 容器”。 输入 Blob 容器的名称。 有关命名 blob 容器的规则和限制的列表，请参阅[创建容器](storage-quickstart-blobs-dotnet.md#create-a-container)部分。 完成后，按 **Enter** 创建 Blob 容器。 成功创建 Blob 容器后，该容器会显示在所选存储帐户的“Blob 容器”文件夹下。

## <a name="upload-blobs-to-the-container"></a>将 blob 上传到容器

Blob 存储支持块 blob、追加 blob 和页 blob。 用于备份 IaaS VM 的 VHD 文件是页 Blob。 追加 blob 用于日志记录，例如有时需要写入到文件，再继续添加更多信息。 Blob 存储中存储的大多数文件都是块 blob。

在容器功能区中，选择“上传”  。 此操作提供上传文件夹或文件的选项。

选择要上传的文件或文件夹。 选择“Blob 类型”  。 可以接受的选项是“追加”、“页”或“块”Blob。

如果上传 .vhd 或 .vhdx 文件，请选择“将 .vhd/.vhdx 文件作为页 Blob 上传(推荐)”。

在“上传到文件夹(可选)”字段中输入一个文件夹名称，用于在容器下的文件夹中存储文件或文件夹。 如果没有选择任何文件夹，文件会直接上传到容器下。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-upload-blob.png" alt-text="Microsoft Azure 存储资源管理器 - 上传 Blob":::

选择“确定”后，选定的文件会排队等待上传，然后上传每个文件。  上传完成后，结果显示在“活动”窗口中。

## <a name="view-blobs-in-a-container"></a>查看容器中的 Blob

在 **Azure 存储资源管理器** 应用程序的存储帐户下选择一个容器。 主窗格会显示一个列表，包含所选容器中的 Blob。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-list-blobs.png" alt-text="显示如何在 Microsoft Azure 存储资源管理器中选择容器的屏幕截图":::

## <a name="download-blobs"></a>下载 Blob

若要使用 **Azure 存储资源管理器** 下载 Blob，请在选中某个 Blob 的情况下，从功能区选择“下载”。 此时会打开一个文件对话框，用于输入文件名。 选择“保存”，开始将 Blob 下载到本地位置。

## <a name="manage-snapshots"></a>管理快照

Azure 存储资源管理器提供拍摄和管理 Blob [快照](./snapshots-overview.md)的功能。 若要拍摄 Blob 的快照，请右键单击 Blob，然后选择“创建快照”。 若要查看 Blob 的快照，请右键单击 Blob，然后选择“管理快照”。 此时会在当前选项卡中显示 Blob 的快照列表。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-snapshots.png" alt-text="显示如何获取和管理 blob 快照的屏幕截图":::

## <a name="generate-a-shared-access-signature"></a>生成共享访问签名

可使用存储资源管理器生成共享访问签名 (SAS)。 右键单击存储帐户、容器或 Blob，然后选择“获取共享访问签名...”。  选择 SAS URL 的开始时间和过期时间以及权限，然后选择“创建”。 存储资源管理器使用你指定的参数生成 SAS 令牌，并显示该令牌以供进行复制。

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-shared-access-signature.png" alt-text="显示如何生成 SAS 的屏幕截图":::

针对存储帐户创建 SAS 时，存储资源管理器会生成帐户 SAS。 有关帐户 SAS 的详细信息，请参阅[创建帐户 SAS](/rest/api/storageservices/create-account-sas)。

针对容器或 blob 创建 SAS 时，存储资源管理器会生成服务 SAS。 有关服务 SAS 的详细信息，请参阅[创建服务 SAS](/rest/api/storageservices/create-service-sas)。

> [!NOTE]
> 使用存储资源管理器创建 SAS 时，始终会为 SAS 分配存储帐户密钥。 存储资源管理器目前不支持创建用户委派 SAS，这是使用 Azure AD 凭据签名的 SAS。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 **Azure 存储资源管理器** 在本地磁盘和 Azure Blob 存储之间转移文件。 要详细了解如何使用 blob 存储，请继续阅读 blob 存储概述。

> [!div class="nextstepaction"]
> [Azure Blob 存储简介](./storage-blobs-introduction.md)