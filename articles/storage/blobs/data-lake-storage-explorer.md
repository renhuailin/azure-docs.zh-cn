---
title: 将 Azure 存储资源管理器与 Azure Data Lake Storage Gen2 配合使用
description: 使用 Azure 存储资源管理器在启用了分层命名空间 (HNS) 的存储帐户中管理目录和文件以及目录访问控制列表 (ACL)。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5ccef241a37e63467b681d5fd12c65072cb92e58
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626439"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Azure 存储资源管理器管理 Azure Data Lake Storage Gen2 中的目录、文件和 ACL

本文介绍如何使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/) 在启用了分层命名空间 (HNS) 的存储帐户中创建和管理 (acl) 的目录、文件和访问控制列表。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 一个已启用分层命名空间 (HNS) 的存储帐户。 按[这些](../common/storage-account-create.md)说明创建一个。

- 已在本地计算机上安装了 Azure 存储资源管理器。 若要安装适用于 Windows、Macintosh 或 Linux 的 Azure 存储资源管理器，请参阅 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="sign-in-to-storage-explorer"></a>登录到存储资源管理器

首次启动存储资源管理器时，将会显示“Microsoft Azure 存储资源管理器 - 连接”  窗口。 尽管存储资源管理器提供了几种连接到存储帐户的方法，但是目前只有一种方法支持管理 ACL。

|任务|目的|
|---|---|
|添加 Azure 帐户 | 将你重定向到组织的登录页，向 Azure 进行身份验证。 目前，如果想管理和设置 ACL，这是唯一支持的身份验证方法。|
|使用连接字符串或共享访问签名 URI | 可用于通过 SAS 令牌或共享连接字符串直接访问容器或存储帐户。 |
|使用存储帐户名称和密钥| 使用存储帐户的存储帐户名称和密钥连接到 Azure 存储。|

选择“添加 Azure 帐户”  ，并单击“登录”  。遵照屏幕提示登录到 Azure 帐户。

![此屏幕截图显示了 Microsoft Azure 存储资源管理器，并突出显示了“添加 Azure 帐户”选项和“登录”按钮。](media/storage-quickstart-blobs-storage-explorer/connect.png)

完成连接后，将会加载 Azure 存储资源管理器并显示“资源管理器”选项卡。  以下视图可以查看通过 [Azure 存储模拟器](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)、[Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 帐户或 [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 环境配置的所有 Azure 存储帐户和本地存储。

![“Microsoft Azure 存储资源管理器 - 连接”窗口](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>创建容器

容器用来存储目录和文件。 若要创建容器，请展开在前面的步骤中创建的存储帐户。 选择 " **Blob 容器**"，右键单击，然后选择 " **创建 blob 容器**"。 输入容器的名称。 有关对容器进行命名的规则和限制的列表，请参阅[创建容器](storage-quickstart-blobs-dotnet.md#create-a-container)部分。 完成后，请按 **Enter** 创建容器。 成功创建容器后，该容器将显示在所选存储帐户的“Blob 容器”文件夹下。 

![Microsoft Azure 存储资源管理器 - 创建容器](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>创建目录

若要创建目录，请选择在前面的步骤中创建的容器。 在容器功能区中，选择“新建文件夹”  按钮。 输入目录的名称。 完成后，按 **Enter** 以创建目录。 成功创建目录后，它将显示在编辑器窗口中。

![Microsoft Azure 存储资源管理器 - 创建目录](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>将 blob 上传到目录

在 "目录" 功能区上，选择 " **上传** " 按钮。 此操作提供上传文件夹或文件的选项。

选择要上传的文件或文件夹。

![Microsoft Azure 存储资源管理器 - 上传 Blob](media/data-lake-storage-explorer/upload-file.png)

选择“确定”后，选定的文件会排队等待上传，然后上传每个文件。  上传完成后，结果将显示在“活动”窗口中。 

## <a name="view-blobs-in-a-directory"></a>查看目录中的 Blob

在 Azure 存储资源管理器应用程序的存储帐户下选择一个目录  。 主窗格会显示一个列表，包含所选目录中的所有 Blob。

![Microsoft Azure 存储资源管理器 - 列出目录中的所有 Blob](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>下载 Blob

若要使用 **Azure 存储资源管理器** 下载文件，请选择所需的文件，然后在功能区中选择“下载”。  此时将打开文件对话框，可在其中输入文件名。 选择“保存”，开始将文件下载到本地位置。 

<a id="managing-access"></a>

## <a name="manage-acls"></a>管理 Acl

右键单击该容器、目录或文件，然后单击 " **管理访问控制列表**"。  下面的屏幕截图显示了右键单击目录时显示的菜单。

> [!div class="mx-imgBorder"]
> ![右键单击 Azure 存储资源管理器中的目录](./media/data-lake-storage-explorer/manage-access-control-list-option.png)

使用 " **管理访问权限** " 对话框，可以管理所有者和所有者组的权限。 它还可以将新用户和组添加访问控制列表中，然后你可以管理其权限。

> [!div class="mx-imgBorder"]
> !["管理访问" 对话框](./media/data-lake-storage-explorer/manage-access-dialog-box.png)

若要向访问控制列表中添加新用户或组，请选择 " **添加** " 按钮。 然后，输入要添加到列表中的相应 Azure Active Directory (AAD) 条目，然后选择 " **添加**"。  用户或组随即出现在“用户和组:”字段中，然后便可开始管理其权限  。

> [!NOTE]
> 建议的最佳做法是在 AAD 中创建安全组并维护组而不是单个用户的权限。 有关此建议和其他最佳做法的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control-model.md)。

使用复选框控件可以设置访问权限和默认 Acl。 若要详细了解这两种类型的 Acl 之间的差异，请参阅 [Acl 类型](data-lake-storage-access-control.md#types-of-acls)。

<a id="apply-acls-recursively"></a>

## <a name="apply-acls-recursively"></a>以递归方式应用 Acl

您可以对父目录的现有子项以递归方式应用 ACL 项，而无需为每个子项目单独进行这些更改。

若要以递归方式应用 ACL 项，请右键单击容器或目录，然后单击 " **传播访问控制列表**"。  下面的屏幕截图显示了右键单击目录时显示的菜单。

> [!div class="mx-imgBorder"]
> ![右键单击目录，然后选择 "传播访问控制" 设置](./media/data-lake-storage-explorer/propagate-access-control-list-option.png)

## <a name="next-steps"></a>后续步骤

了解 Data Lake Storage Gen2 中的访问控制列表。

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)
