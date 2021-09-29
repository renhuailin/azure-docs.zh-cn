---
title: 将 Azure 存储资源管理器与 Azure Data Lake Storage Gen2 配合使用
description: 使用 Azure 存储资源管理器在启用了分层命名空间 (HNS) 的存储帐户中管理目录和文件以及目录访问控制列表 (ACL)。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5307a56ba2384f9e0294634530823238fd903859
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766742"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>使用 Azure 存储资源管理器管理 Azure Data Lake Storage Gen2 中的目录、文件

本文介绍了如何使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)在启用了分层命名空间 (HNS) 的存储帐户中创建和管理目录和文件。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 已启用分层命名空间 (HNS) 的存储帐户。 按[这些](../common/storage-account-create.md)说明创建一个。

- 已在本地计算机上安装了 Azure 存储资源管理器。 若要安装适用于 Windows、Macintosh 或 Linux 的 Azure 存储资源管理器，请参阅 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

> [!NOTE]
> 在使用 Azure Data Lake Storage Gen2 时，存储资源管理器会使用 Blob (blob) 和 Data Lake Storage Gen2 (dfs) [终结点](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage)。 如果使用专用终结点配置对 Azure Data Lake Storage Gen2 的访问，请确保为存储帐户创建两个专用终结点：一个具有目标子资源 `blob`，另一个具有目标子资源 `dfs`。

## <a name="sign-in-to-storage-explorer"></a>登录到存储资源管理器

首次启动存储资源管理器时，将会显示“Microsoft Azure 存储资源管理器 - 连接”  窗口。 尽管存储资源管理器提供了几种连接到存储帐户的方法，但是目前只有一种方法支持管理 ACL。

|任务|目的|
|---|---|
|添加 Azure 帐户 | 将你重定向到组织的登录页，向 Azure 进行身份验证。 目前，如果想管理和设置 ACL，这是唯一支持的身份验证方法。|
|使用连接字符串或共享访问签名 URI | 可用于通过 SAS 令牌或共享连接字符串直接访问容器或存储帐户。 |
|使用存储帐户名称和密钥| 使用存储帐户的存储帐户名称和密钥连接到 Azure 存储。|

选择“添加 Azure 帐户”  ，并单击“登录”  。遵照屏幕提示登录到 Azure 帐户。

![此屏幕截图显示了 Microsoft Azure 存储资源管理器，并突出显示了“添加 Azure 帐户”选项和“登录”按钮。](media/quickstart-storage-explorer/storage-explorer-connect.png)

完成连接后，将会加载 Azure 存储资源管理器并显示“资源管理器”选项卡。  以下视图可以查看通过 [Azure 存储模拟器](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)、[Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 帐户或 [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 环境配置的所有 Azure 存储帐户和本地存储。

![“Microsoft Azure 存储资源管理器 - 连接”窗口](media/quickstart-storage-explorer/storage-explorer-main-page.png)

## <a name="create-a-container"></a>创建容器

容器用来存储目录和文件。 若要创建容器，请展开在前面的步骤中创建的存储帐户。 选择并右键单击“Blob 容器”，然后选择“创建 Blob 容器”。  输入容器的名称。 有关对容器进行命名的规则和限制的列表，请参阅[创建容器](storage-quickstart-blobs-dotnet.md#create-a-container)部分。 完成后，请按 **Enter** 创建容器。 成功创建容器后，该容器将显示在所选存储帐户的“Blob 容器”文件夹下。 

![Microsoft Azure 存储资源管理器 - 创建容器](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>创建目录

若要创建目录，请选择在前面的步骤中创建的容器。 在容器功能区中，选择“新建文件夹”  按钮。 输入目录的名称。 完成后，按 **Enter** 以创建目录。 成功创建目录后，它将显示在编辑器窗口中。

![Microsoft Azure 存储资源管理器 - 创建目录](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>将 blob 上传到目录

在目录功能区上，选择“上传”按钮。 此操作提供上传文件夹或文件的选项。

选择要上传的文件或文件夹。

![Microsoft Azure 存储资源管理器 - 上传 Blob](media/data-lake-storage-explorer/upload-file.png)

选择“确定”后，选定的文件会排队等待上传，然后上传每个文件。  上传完成后，结果将显示在“活动”窗口中。 

## <a name="view-blobs-in-a-directory"></a>查看目录中的 Blob

在 Azure 存储资源管理器应用程序的存储帐户下选择一个目录  。 主窗格会显示一个列表，包含所选目录中的所有 Blob。

![Microsoft Azure 存储资源管理器 - 列出目录中的所有 Blob](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>下载 Blob

若要使用 **Azure 存储资源管理器** 下载文件，请选择所需的文件，然后在功能区中选择“下载”。  此时将打开文件对话框，可在其中输入文件名。 选择“保存”，开始将文件下载到本地位置。 

## <a name="next-steps"></a>后续步骤

了解如何通过设置访问控制列表 (ACL) 来管理文件和目录权限

> [!div class="nextstepaction"]
> [使用 Azure 存储资源管理器在 Azure Data Lake Storage Gen2 中管理 ACL](./data-lake-storage-explorer-acl.md)
