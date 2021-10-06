---
title: 存储资源管理器：在 Azure Data Lake Storage Gen2 中设置 ACL
description: 使用 Azure 存储资源管理器在启用了分层命名空间 (HNS) 的存储帐户中管理访问控制列表 (ACL)。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5fc2fbf2b0f55f356f6a2f5c4a3eb0fd2c527449
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545615"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>使用 Azure 存储资源管理器在 Azure Data Lake Storage Gen2 中管理 ACL

本文介绍如何使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)在启用了分层命名空间 (HNS) 的存储帐户中管理访问控制列表 (ACL)。

可以使用存储资源管理器来查看然后更新目录和文件的 ACL。 ACL 继承已可用于在父目录下创建的新子项。 但还可为父目录的现有子项以递归方式应用 ACL 设置，而不必为每个子项单独进行这些更改。

本文介绍如何修改文件或目录的 ACL，以及如何以递归方式将 ACL 设置应用于子目录。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 已启用分层命名空间 (HNS) 的存储帐户。 按[这些](../common/storage-account-create.md)说明创建一个。

- 已在本地计算机上安装了 Azure 存储资源管理器。 若要安装适用于 Windows、Macintosh 或 Linux 的 Azure 存储资源管理器，请参阅 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

- 必须具备以下安全权限中的一种：

  - 你的用户标识在目标容器、存储帐户、父资源组或订阅范围中已分配有[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色。

  - 你是目标容器、目录或 Blob 的所有者用户，并且你计划在其中应用 ACL 设置。

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

## <a name="manage-an-acl"></a>管理 ACL

右键单击容器、目录或文件，然后单击“管理访问控制列表”。  下面的屏幕截图显示了右键单击目录时出现的菜单。

> [!div class="mx-imgBorder"]
> ![在 Azure 存储资源管理器中右键单击目录](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

“管理访问权限”对话框可以管理所有者和所有者组的权限。 它还可以将新用户和组添加访问控制列表中，然后你可以管理其权限。

> [!div class="mx-imgBorder"]
> ![“管理访问权限”对话框](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

要将新用户或组添加到访问控制列表中，请选择“添加”按钮。 然后输入要添加到列表中的相应 Azure Active Directory (Azure AD) 条目，再选择“添加”。  用户或组随即出现在“用户和组:”字段中，然后便可开始管理其权限  。

> [!NOTE]
> 建议的最佳做法是在 Azure AD 中创建安全组并维护组（而不是单个用户）的权限。 有关此建议以及其他最佳做法的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-explorer-acl.md)。

使用复选框控件可以设置访问权限和默认 ACL。 若要详细了解这些 ACL 类型之间的差异，请参阅 [ACL 类型](data-lake-storage-access-control.md#types-of-acls)。

## <a name="apply-acls-recursively"></a>以递归方式应用 ACL

可以为父目录的现有子项以递归方式应用 ACL 条目，而不必为每个子项单独进行这些更改。

若要以递归方式应用 ACL 条目，请右键单击容器或目录，然后单击“传播访问控制列表”。  下面的屏幕截图显示了右键单击目录时出现的菜单。

> [!div class="mx-imgBorder"]
> ![右键单击目录，并选择“传播访问控制”设置](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>后续步骤

了解 Data Lake Storage Gen2 权限模型。

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2 中的访问控制模型](./data-lake-storage-access-control-model.md)
