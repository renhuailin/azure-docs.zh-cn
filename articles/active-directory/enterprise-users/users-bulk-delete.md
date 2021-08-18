---
title: 在 Azure Active Directory 门户中批量删除用户 | Microsoft Docs
description: 在 Azure Active Directory 中的 Azure 管理中心批量删除用户
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 07/09/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdaa91a5ffed9e75666602490b4829a3a95e2782
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113597822"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>批量删除 Azure Active Directory 中的用户

在 Azure Active Directory (Azure AD) 门户中，可使用逗号分隔值 (CSV) 文件批量删除用户，从而将大量成员从组中删除。

## <a name="csv-template-structure"></a>CSV 模板结构

![CSV 文件包含要删除的用户的名称和 ID](./media/users-bulk-delete/delete-csv-file.png)

下载的 CSV 模板中的行如下所示：

- **版本号**：包含版本号的第一行必须包含在上传的 CSV 中。
- **列标题**：`User name [userPrincipalName] Required`。 较旧版本的模板可能会有所不同。
- **示例行**：我们已在模板中包含可接受值的示例。 `Example: chris@contoso.com` 你必须删除示例行并将其替换为自己的项。

### <a name="additional-guidance"></a>其他指南

- 不得删除或修改模板的前两行，否则无法处理模板。
- 所需的列会先列出。
- 请勿将新列添加到模板。 所添加的任何其他列都会被忽略，不进行处理。
- 在执行新更改之前，请下载最新版本的 CSV 模板。

## <a name="to-bulk-delete-users"></a>批量删除用户

1. 使用组织中的用户管理员帐户[登录到 Azure AD 组织](https://aad.portal.azure.com)。
1. 在 Azure AD 中，选择“用户” > “批量操作” > “批量删除”  。
1. 在“批量删除用户”页面中，选择“下载”以下载最新版本的 CSV 模板 。
1. 打开 CSV 文件，并为每个要删除的用户添加一行。 唯一需要的值为“用户主体名称”。 保存文件。
1. 在“批量删除用户”页的“上传 csv 文件”下，浏览到该文件。 选择该文件并单击“提交”后，将启动对 CSV 文件的验证。
1. 验证文件内容后，会看到“文件上传成功”消息。 如果有错误，必须修正错误，然后才能提交作业。
1. 文件通过验证后，请选择“提交”，以启动用于删除用户的 Azure 批量操作。
1. 删除操作完成后，会显示一条通知，指出批量操作成功。

如果有错误，可以在“批量操作结果”页下载并查看结果文件。 该文件包含每个错误的原因。

## <a name="check-status"></a>查看状态

可在“批量操作结果”页中查看所有挂起的批量请求的状态。

   [![在“批量操作结果”页中查看删除状态。](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

接下来，可通过 Azure 门或 PowerShell 查看已删除的用户是否存在于 Azure AD 组织中。

## <a name="verify-deleted-users-in-the-azure-portal"></a>在 Azure 门户中验证已删除的用户

1. 使用组织中的用户管理员的帐户登录到 Azure 门户。
1. 在导航窗格中选择“Azure Active Directory”。
1. 在“管理”下，选择“用户” 。
1. 在“显示”下仅选择“所有用户”，验证已删除的用户是否不再列出。

### <a name="verify-deleted-users-with-powershell"></a>使用 PowerShell 验证已删除的用户

运行以下命令：

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

验证已删除的用户是否不再列出。

## <a name="next-steps"></a>后续步骤

- [批量添加用户](users-bulk-add.md)
- [下载用户列表](users-bulk-download.md)
- [批量还原用户](users-bulk-restore.md)
