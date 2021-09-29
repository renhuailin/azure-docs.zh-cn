---
title: 使用 Azure 门户管理 Azure Data Lake Storage Gen2 中的 ACL
description: 使用 Azure 门户管理已启用分层命名空间 (HNS) 的存储帐户中的管理访问控制列表 (ACL)。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: bb9404351142e60e242e1e4181961c1ed4609012
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564971"
---
# <a name="use-the-azure-portal-to-manage-acls-in-azure-data-lake-storage-gen2"></a>使用 Azure 门户管理 Azure Data Lake Storage Gen2 中的 ACL

本文介绍如何使用 [Azure 门户](https://ms.portal.azure.com/)来管理已启用分层命名空间功能的存储帐户中目录或 Blob 的访问控制列表 (ACL)。

有关 ACL 结构的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制列表 (ACL)](data-lake-storage-access-control.md)。

若要了解如何结合使用 ACL 和 Azure 角色，请参阅 [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control-model.md)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 已启用分层命名空间功能的存储帐户。 按[这些](create-data-lake-storage-account.md)说明创建一个。

- 必须具备以下安全权限中的一种：

  - 你的用户标识在目标容器、存储帐户、父资源组或订阅范围中已分配有[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色。

  - 你是目标容器、目录或 Blob 的所有者用户，并且你计划在其中应用 ACL 设置。

## <a name="manage-an-acl"></a>管理 ACL

1. 登录到 [Azure 门户](https://portal.azure.com/)即可开始操作。

2. 找到存储帐户并显示帐户概览。

3. 在“数据存储”下选择“容器” 。

   存储帐户中的容器随即显示。

   > [!div class="mx-imgBorder"]
   > ![Azure 门户中存储帐户容器的位置](./media/data-lake-storage-acl-azure-portal/find-containers-in-azure-portal.png)

5. 导航到任意容器、目录或 Blob。 右键单击对象，然后选择“管理 ACL”。

   > [!div class="mx-imgBorder"]
   > ![用于管理 ACL 的上下文菜单](./media/data-lake-storage-acl-azure-portal/manage-acl-menu-item.png)

   “管理 ACL”页面的“访问权限”选项卡随即显示 。 使用此选项卡中的控制来管理对对象的访问。

   > [!div class="mx-imgBorder"]
   > ![“管理 ACL”页面的访问 ACL 选项卡](./media/data-lake-storage-acl-azure-portal/access-acl-page.png)

7. 若要将安全主体添加到 ACL，请选择“添加主体”按钮。

   > [!TIP]
   > 安全主体是一个对象，表示在 Azure Active Directory (AD) 中定义的用户、组、服务主体或托管标识。

   使用搜索框查找安全主体，然后单击“选择”按钮。

   > [!div class="mx-imgBorder"]
   > ![将安全主体添加到 ACL](./media/data-lake-storage-acl-azure-portal/get-security-principal.png)

   > [!NOTE]
   > 建议在 Azure AD 中创建安全组，然后维护该组的权限，而不是维护各用户的权限。 有关此建议以及其他最佳做法的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control-model.md)。

8. 若要管理默认 ACL，选择“默认权限”选项卡，然后选中“配置默认权限”复选框 。

   > [!TIP]
   > 默认 ACL 是 ACL 的模板，可确定目录下创建的任何子项的访问 ACL。 Blob 没有默认 ACL，因此此选项卡仅针对目录显示。

   > [!div class="mx-imgBorder"]
   > ![“管理 ACL”页面的“默认 ACL”选项卡](./media/data-lake-storage-acl-azure-portal/default-acl-page.png)

## <a name="apply-an-acl-recursively"></a>以递归方式应用 ACL

可以为父目录的现有子项以递归方式应用 ACL 条目，而不必为每个子项单独进行这些更改。 但不能通过 Azure 门户以递归方式应用 ACL 条目。

若要以递归方式应用 ACL，请使用 Azure 存储资源管理器、PowerShell 或 Azure CLI。 如果希望编写代码，还可以使用 .NET、Java、Python 或 Node.js API。

可以通过下文查找完整的指南列表：[如何设置 ACL](data-lake-storage-access-control.md#how-to-set-acls)。

## <a name="next-steps"></a>后续步骤

了解 Data Lake Storage Gen2 权限模型。

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2 中的访问控制模型](./data-lake-storage-access-control-model.md)
