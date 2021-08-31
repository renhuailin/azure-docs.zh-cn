---
title: 将 Azure 标识资源从 Azure 德国迁移到全球 Azure
description: 本文介绍如何将 Azure 标识资源从 Azure 德国迁移到全球 Azure。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 41f8019d8732560754be4757845f22f436cfcf70
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029081"
---
# <a name="migrate-identity-resources-to-global-azure"></a>将标识资源迁移到全球 Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

本文中的信息可帮助你将 Azure 标识资源从 Azure 德国迁移到全球 Azure。

有关标识/租户的指南仅适用于 Azure 客户。 如果 Azure 和 Microsoft 365（或其他 Microsoft 产品）使用常用 Azure Active Directory (Azure AD) 租户，则标识的迁移比较复杂，应在使用此迁移指南之前首先联系你的帐户管理员。

## <a name="azure-active-directory"></a>Azure Active Directory

Azure 德国中的 Azure AD 独立于全球 Azure 中的 Azure AD。 目前，无法将 Azure AD 用户从 Azure 德国移动到全球 Azure。

Azure 德国和全球 Azure 中的默认租户名称始终不同，因为 Azure 会根据环境自动追加后缀。 例如，全球 Azure 中的 contoso 租户的成员用户名为 user1\@contoso.microsoftazure.com。 而在 Azure 德国中则为 user1\@contoso.microsoftazure.de。

在 Azure AD 中使用自定义域名（例如 contoso.com）时，必须在 Azure 中注册此域名。 自定义域名一次只能在一个云环境中定义。 当某个域已在 Azure Active Directory 的任何一个实例中注册时，域验证就会失败。 例如，Azure 德国中的用户 user1\@contoso.com 不能同时以同一个名称存在于全球 Azure 中。 注册 contoso.com 就会失败。

在“软”迁移中，某些用户已在新环境中，而某些用户仍在旧环境中，这种迁移需要针对不同的云环境使用不同的登录名。

本文未涵盖所有可能的迁移方案。 例如，建议的方案取决于预配用户的方式、不同用户名或 UserPrincipalNames 的使用选项以及其他依赖项。 但是，我们编译了一些提示，以帮助你盘存当前环境中的用户和组。

若要获取与 Azure AD 相关的所有 cmdlet 的列表，请运行：

```powershell
Get-Help Get-AzureAD*
```

### <a name="inventory-users"></a>盘存用户

若要大致了解你的 Azure AD 实例中存在的所有用户和组：

```powershell
Get-AzureADUser -All $true
```

若要仅列出已启用的帐户，请添加以下筛选器：

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true}
```

若要完整转储所有属性，以防忘记某些内容：

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | Format-List *
```

若要选择重新创建用户所需的属性：

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | select UserPrincipalName,DisplayName,GivenName,Surname
```

若要将列表导出到 Excel，请在此列表末尾处使用 Export-Csv cmdlet。 完整的导出内容可能如以下示例所示：

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | select UserPrincipalName,DisplayName,GivenName,Surname | Export-Csv -Path c:\temp\alluserUTF8.csv -Delimiter ";" -Encoding UTF8
```

> [!NOTE]
> 密码无法迁移。 相反，必须根据你的方案分配新密码或使用自助服务机制。
>
>此外，你可能需要根据环境收集其他信息，例如 Extensions、DirectReport 或 LicenseDetail 的值。  

根据需要格式化 CSV 文件。 然后按照[从 CSV 导入数据](/powershell/azure/active-directory/importing-data)中所述的步骤在你的新环境中重新创建用户。

### <a name="inventory-groups"></a>盘存组

若要记录组成员身份：

```powershell
Get-AzureADGroup
```

若要获取每个组的成员列表：

```powershell
Get-AzureADGroup | ForEach-Object {$_.DisplayName; Get-AzureADGroupMember -ObjectId $_.ObjectId}
```

### <a name="inventory-service-principals-and-applications"></a>盘存服务主体和应用程序

尽管必须重新创建所有服务主体和应用程序，但最好记录服务主体和应用程序的状态。 可以使用以下 cmdlet 获取所有服务主体的详细列表：

```powershell
Get-AzureADServicePrincipal |Format-List *
```

```powershell
Get-AzureADApplication |Format-List *
```

可以使用以 `Get-AzureADServicePrincipal*` 或 `Get-AzureADApplication*` 开头的其他 cmdlet 获取详细信息。 

### <a name="inventory-directory-roles"></a>盘存目录角色

若要记录当前角色分配：

```powershell
Get-AzureADDirectoryRole
```

演练每个角色以查找与该角色关联的用户或应用程序：

```powershell
Get-AzureADDirectoryRole | ForEach-Object {$_.DisplayName; Get-AzureADDirectoryRoleMember -ObjectId
$_.ObjectId | Format-Table}
```
参考信息：

- 了解[混合标识解决方案](../active-directory/hybrid/whatis-hybrid-identity.md)。
- 阅读博客文章[配合使用 Azure AD Connect 和多个云](/archive/blogs/ralfwi/using-adconnect-with-multiple-clouds)，了解如何同步到不同云环境。
- 详细了解 [Azure Active Directory](../active-directory/index.yml)。
- 阅读了解[自定义域名](../active-directory/fundamentals/add-custom-domain.md)。
- 了解如何[将数据从 CSV 导入到 Azure AD](/powershell/azure/active-directory/importing-data)。

## <a name="azure-ad-connect"></a>Azure AD Connect

Azure AD Connect 是一种在本地 Active Directory 实例和 Azure Active Directory (Azure AD) 之间同步标识数据的工具。 当前版本的 Azure AD Connect 对 Azure 德国和全球 Azure 均适用。 Azure AD Connect 一次只能同步到一个 Azure AD 实例。 如果希望同时同步到 Azure 德国和全球 Azure，请考虑使用以下选项：

- 为 Azure AD Connect 的第二个实例使用额外的服务器。 同一台服务器上不能有多个 Azure AD Connect 实例。
- 为用户定义新的登录名。 登录名的域部分（ **\@** 后）必须在每个环境中各不相同。
- 此外，向后同步（从 Azure AD 同步到本地 Active Directory）时，还需定义清晰的“可信源”。

如果已使用 Azure AD Connect 同步 Azure 德国，请确保迁移所有手动创建的用户。 以下 PowerShell cmdlet 列出了使用 Azure AD Connect 未同步的所有用户：

```powershell
Get-AzureADUser -All $true |Where-Object {$_.DirSyncEnabled -ne "True"}
```

参考信息：

- 详细了解 [Azure AD Connect](../active-directory/hybrid/reference-connect-dirsync-deprecated.md)。

## <a name="multi-factor-authentication"></a>多重身份验证

必须在新环境中重新创建用户和重新定义 Azure AD 多重身份验证实例。 

若要获取启用或强制实施多重身份验证的用户帐户的列表，请执行以下操作：

1. 登录到 Azure 门户。
1. 选择“用户” > “所有用户” > “多重身份验证”。  
1. 重定向到多重身份验证服务页时，设置相应的筛选器以获取用户列表。

参考信息：

- 详细了解 [Azure AD 多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)。

## <a name="next-steps"></a>后续步骤

了解用于在以下服务类别中迁移资源的工具、方法和建议：

- [计算](./germany-migration-compute.md)
- [联网](./germany-migration-networking.md)
- [存储](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [数据库](./germany-migration-databases.md)
- [分析](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [集成](./germany-migration-integration.md)
- [安全性](./germany-migration-security.md)
- [管理工具](./germany-migration-management-tools.md)
- [介质](./germany-migration-media.md)