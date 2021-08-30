---
title: 使用 PowerShell 或 Graph 浏览器管理 Azure AD 角色的先决条件 - Azure Active Directory
description: 使用 PowerShell 或 Graph 浏览器管理 Azure Active Directory 角色的先决条件。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 08/06/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef457948a28d9d07464b96bfaea4df6a6f2a8723
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747854"
---
# <a name="prerequisites-to-use-powershell-or-graph-explorer-for-azure-ad-roles"></a>使用 PowerShell 或 Graph 浏览器管理 Azure AD 角色的先决条件

如果要使用 PowerShell 或 Graph 浏览器管理 Azure Active Directory (Azure AD) 角色，用户必须满足所需先决条件。 本文针对不同 Azure AD 角色功能介绍使用 PowerShell 和 Graph 浏览器的先决条件。

## <a name="azuread-module"></a>AzureAD 模块

若要使用 PowerShell 命令执行以下操作：

- 列出角色分配
- 创建可分配角色的组
- 对管理单元进行管理

必须安装以下模块：

- [AzureAD](https://www.powershellgallery.com/packages/AzureAD) 2.0.2.137 或更高版本


#### <a name="check-azuread-version"></a>检查 AzureAD 版本

若要检查已安装的 AzureAD 版本，请使用 [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule)。

```powershell
Get-InstalledModule -Name AzureAD
```

应会看到如下所示的输出：

```powershell
Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.2.137  AzureAD                             PSGallery            Azure Active Directory V2 General Availability M...
```

#### <a name="install-azuread"></a>安装 AzureAD

如果尚未安装 AzureAD，请使用 [Install-Module](/powershell/module/powershellget/install-module) 安装 AzureAD。

```powershell
Install-Module -Name AzureAD
```

#### <a name="update-azuread"></a>更新 AzureAD

若要将 AzureAD 更新为最新版本，请重新运行 [Install-Module](/powershell/module/powershellget/install-module)。

```powershell
Install-Module -Name AzureAD
```

#### <a name="use-azuread"></a>使用 AzureAD

若要使用 AzureAD，请执行以下步骤，确保将其导入当前会话。

1. 使用 [Get-Module](/powershell/module/microsoft.powershell.core/get-module) 检查 AzureAD 是否已加载到内存中。

    ```powershell
    Get-Module -Name AzureAD
    ```

1. 如果在上一步中看不到任何输出，请使用 [Import-Module](/powershell/module/microsoft.powershell.core/import-module) 导入 AzureAD。 `-Force` 参数会删除已加载的模块，然后再次进行导入。

    ```powershell
    Import-Module -Name AzureAD -Force
    ```

1. 再次运行 [Get-Module](/powershell/module/microsoft.powershell.core/get-module)。

    ```powershell
    Get-Module -Name AzureAD
    ```

    应会看到如下所示的输出：
    
    ```powershell
    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     2.0.2.137  AzureAD                             {Add-AzureADApplicationOwner, Add-AzureADDeviceRegisteredO...
    ```

## <a name="azureadpreview-module"></a>AzureADPreview 模块

若要使用 PowerShell 命令执行以下操作：

- 向用户或组分配角色
- 删除角色分配
- 使用 Privileged Identity Management 使某组符合某角色条件
- 创建自定义角色

必须安装以下模块：

- [AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview) 2.0.2.138 或更高版本


#### <a name="check-azureadpreview-version"></a>检查 AzureADPreview 版本

若要检查已安装的 AzureADPreview 版本，请使用 [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule)。

```powershell
Get-InstalledModule -Name AzureADPreview
```

应会看到如下所示的输出：

```powershell
Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.2.138  AzureADPreview                      PSGallery            Azure Active Directory V2 Preview Module. ...
```

#### <a name="install-azureadpreview"></a>安装 AzureADPreview

如果尚未安装 AzureADPreview，请使用 [Install-Module](/powershell/module/powershellget/install-module) 安装 AzureADPreview。

```powershell
Install-Module -Name AzureADPreview
```

#### <a name="update-azureadpreview"></a>更新 AzureADPreview

若要将 AzureADPreview 更新为最新版本，请重新运行 [Install-Module](/powershell/module/powershellget/install-module)。

```powershell
Install-Module -Name AzureADPreview
```

#### <a name="use-azureadpreview"></a>使用 AzureADPreview

若要使用 AzureADPreview，请执行以下步骤，确保将其导入当前会话。

1. 使用 [Get-Module](/powershell/module/microsoft.powershell.core/get-module) 检查 AzureADPreview 是否已加载到内存中。

    ```powershell
    Get-Module -Name AzureADPreview
    ```

1. 如果在上一步中看不到任何输出，请使用 [Import-Module](/powershell/module/microsoft.powershell.core/import-module) 导入 AzureADPreview。 `-Force` 参数会删除已加载的模块，然后再次进行导入。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. 再次运行 [Get-Module](/powershell/module/microsoft.powershell.core/get-module)。

    ```powershell
    Get-Module -Name AzureADPreview
    ```

    应该会看到与下面类似的输出：
    
    ```powershell
    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     2.0.2.138  AzureADPreview                      {Add-AzureADAdministrativeUnitMember, Add-AzureADApplicati...
    ```

## <a name="graph-explorer"></a>Graph 浏览器

若要使用 [Microsoft Graph API](/graph/overview) 和 [Graph 浏览器](/graph/graph-explorer/graph-explorer-overview)管理 Azure AD 角色，必须执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “企业应用程序”。 

1. 在应用程序列表中，找到并选择“Graph 浏览器”。

1. 选择“权限”。

1. 选择“为 Graph 浏览器授予管理员同意”。

    ![显示“为 Graph 浏览器授予管理员同意”链接的屏幕截图。](./media/prerequisites/select-graph-explorer.png)

1. 使用 [Graph 浏览器工具](https://aka.ms/ge)。

## <a name="next-steps"></a>后续步骤

- [安装用于 Graph 的 Azure Active Directory PowerShell](/powershell/azure/active-directory/install-adv2)
- [AzureAD 模块文档](/powershell/module/azuread/)
- [图表资源管理器](/graph/graph-explorer/graph-explorer-overview)
