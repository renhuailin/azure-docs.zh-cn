---
title: 针对 StorSimple 使用 Azure 基于角色的访问控制 | Microsoft Docs
description: 介绍如何在 StorSimple 上下文中使用 Azure 基于角色的访问控制 (Azure RBAC)。
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 49c38e23ddbbfe983ff82ad25363c744292d4d69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518970"
---
# <a name="azure-role-based-access-control-for-storsimple"></a>适用于 StorSimple 的 Azure 基于角色的访问控制

本文将简要说明如何对 StorSimple 设备使用 Azure 基于角色的访问控制 (Azure RBAC)。 Azure RBAC 提供针对 Azure 的细化访问管理。 使用 Azure RBAC 只向 StorSimple 用户授予其执行作业所需的适当程度的访问权限，而不是向每个人授予不受限制的访问权限。 有关 Azure 中访问管理基础知识的详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)。

本文适用于在 Azure 门户中运行 Update 3.0 或更高版本的 StorSimple 8000 系列设备。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-roles-for-storsimple"></a>适用于 StorSimple 的 Azure 角色

Azure RBAC 可基于角色进行分配。 该角色可确保根据环境中的可用资源授予特定的权限级别。 StorSimple 用户可以选择以下两种类型的角色：内置或自定义。

* **内置角色** - 内置角色可以是所有者、参与者、读者或用户访问管理员。 有关详细信息，请参阅 [Azure 基于角色的访问控制的内置角色](../role-based-access-control/built-in-roles.md)。

* 自定义角色 - 如果内置角色无法满足需求，可创建适用于 StorSimple 的 Azure 自定义角色。 要创建 Azure 自定义角色，首先需使用某个内置角色，对其进行编辑，然后将其导回环境中。 使用 Azure PowerShell 或 Azure CLI 管理角色的下载和上传。 有关详细信息，请参阅[针对基于角色的访问控制创建自定义角色](../role-based-access-control/custom-roles.md)。

若要在 Azure 门户中查看可用于 StorSimple 设备用户的不同角色，请转到 StorSimple 设备管理器服务，然后转到“访问控制(IAM)”>“角色”。


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>为 StorSimple 基础结构管理员创建自定义角色

在以下示例中，我们首先使用内置角色“读者”，该角色可使用户查看所有资源范围，但不能对其进行编辑或创建新的范围。 然后，扩展此角色以创建新的自定义角色 StorSimple 基础结构管理员。将此角色分配给可以管理 StorSimple 设备基础结构的用户。

1. 以管理员身份运行 Windows PowerShell。

2. 登录 Azure。

    `Connect-AzAccount`

3. 在计算机上将读者角色导出为 JSON 模板。

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. 在 Visual Studio 中打开 JSON 文件。 可看到一个典型的 Azure 角色主要由三部分组成：Actions、NotActions 和 AssignableScopes  。

    Actions 节列出了允许此角色执行的所有操作。 每个操作都是从资源提供程序分配的。 对于 StorSimple 基础结构管理员，请使用 `Microsoft.StorSimple` 资源提供程序。

    使用 PowerShell 可查看订阅中可用且注册的所有资源提供程序。

    `Get-AzResourceProvider`

    还可查看可用于管理资源提供程序的所有 PowerShell cmdlet。

    NotActions 部分将列出特定 Azure 角色的所有受限制操作。 在此示例中，不会限制任何操作。
    
    AssignableScopes 下列出了订阅 ID。 请确保 Azure 角色包含应用它的显式订阅 ID。 如果未指定正确的订阅 ID，则不能导入订阅中的角色。

    编辑文件，同时记住上述注意事项。

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. 将 Azure 自定义角色导回环境中。

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


此角色现应显示在“访问控制”边栏选项卡的角色列表中。

![查看 Azure 角色](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

有关详细信息，请转到[客户角色](../role-based-access-control/custom-roles.md)。

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>通过 PowerShell 创建自定义角色的示例输出

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>将用户添加到自定义角色

授予资源、资源组或订阅（即角色分配范围）内的访问权限。 提供访问权限时，请牢记在父节点上授予的访问权限会由子节点继承。 有关详细信息，请访问 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)。

1. 请转到“访问控制(IAM)”。 在“访问控制”边栏选项卡上单击“+ 添加”。

    ![为 Azure 角色添加访问权限](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. 选择要分配的角色，在本例中为“StorSimple 基础结构管理员”。

3. 在你想要授予访问权限的目录中选择用户、组或应用程序。 可以通过显示名称、电子邮件地址和对象标识符搜索该目录。

4. 选择“保存”创建分配。

    ![为 Azure 角色添加权限](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

“添加用户”通知会跟踪进度。 成功添加用户后，访问控制中的用户列表会进行更新。

## <a name="view-permissions-for-the-custom-role"></a>查看自定义角色的权限

创建此角色后，可在 Azure 门户中查看与此角色关联的权限。

1. 若要查看与此角色关联的权限，请转到“访问控制(IAM)”>“角色”>“StorSimple 基础结构管理员”。随即显示此角色中的用户列表。

2. 选择 StorSimple 基础结构管理员用户，然后单击“权限”。

    ![查看 StorSimple 基础结构管理员角色的权限](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. 随即显示与此角色关联的权限。

    ![查看 StorSimple 基础结构管理员角色中的用户](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>后续步骤

了解如何[为内部用户和外部用户分配自定义角色](../role-based-access-control/role-assignments-external-users.md)。
