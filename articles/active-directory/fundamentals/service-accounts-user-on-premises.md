---
title: 保护基于用户的服务帐户 |Azure Active Directory
description: 有关保护本地用户帐户的指南。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416973"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>在 Active Directory 中保护基于用户的服务帐户

本地用户帐户是保护在 Windows 上运行的服务的传统方法。 如果你的服务不支持全局托管服务帐户 (Gmsa) 和独立托管服务帐户 (sMSAs) ，则使用这些帐户作为最后手段。 有关选择要使用的最佳帐户类型的信息，请参阅本地服务帐户概述。 还应调查是否可以将服务移到使用 Azure 服务帐户（如托管标识或服务主体）。 

可以创建本地用户帐户，以便为服务提供安全上下文，并授予服务访问本地和网络资源所需的权限。 它们需要手动密码管理，与任何其他 Active Directory (AD) 用户帐户相同。 服务和域管理员需要遵守强密码管理过程，以确保这些帐户的安全。

使用用户帐户作为服务帐户时，仅将其用于单个服务。 将其命名为可以清楚地表明它是一个服务帐户和哪个服务。 

## <a name="benefits-and-challenges"></a>优点和挑战

优点

本地用户帐户是用于服务的最通用的帐户类型。 用作服务帐户的用户帐户可以由所有策略控制正常的用户帐户。 也就是说，仅当无法使用 MSA 时才使用它们。 同时评估计算机帐户是否是更好的选择。 

本地用户帐户的挑战

以下挑战与本地用户帐户的使用相关。

| 挑战| 缓解措施 |
| - | - |
| 密码管理是一个手动过程，可能会导致安全性较差和服务停机。| 确保密码复杂性和密码更改受可靠的过程的管辖，以确保使用强密码进行定期更新。 <br> 使用服务上的密码更新来协调密码更改，这将导致服务停机。 |
| 标识充当服务帐户的本地用户帐户可能会很困难。| 记录并维护在你的环境中部署的服务帐户的记录。 <br> 跟踪向其分配访问权限的帐户名和资源。 <br> 请考虑将 svc_ 的前缀添加到用作服务帐户的所有用户帐户。 |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>查找用作服务帐户的本地用户帐户

本地用户帐户与其他 AD 用户帐户一样。 因此，查找这些帐户可能会很困难，因为用户帐户不存在将其标识为服务帐户的单个属性。 

建议为用作服务帐户的任何用户帐户创建一个易于识别的命名约定。

例如，将 "服务-" 添加为前缀，并将服务命名为 "HRDataConnector"。

你可以使用以下一些指标来查找这些服务帐户，但是，这可能找不到所有此类帐户。

* 信任委派的帐户。

* 具有服务主体名称的帐户。

* 其密码设置为永不过期的帐户。

可以运行以下 PowerShell 命令来查找为服务创建的本地用户帐户。

### <a name="find-accounts-trusted-for-delegation"></a>查找受信任的委派帐户

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>查找具有服务主体名称的帐户

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>查找密码设置为永不过期的帐户

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


你还可以审核对敏感资源的访问，并将审核日志存档到安全信息和事件管理 (SIEM) 系统。 使用 Azure Log Analytics 或 Azure Sentinel 等系统，可以搜索并分析和服务帐户。

## <a name="assess-security-of-on-premises-user-accounts"></a>评估本地用户帐户的安全性

使用以下条件评估用作服务帐户的本地用户帐户的安全性：

* 什么是密码管理策略？

* 帐户是否是任何特权组的成员？

* 此帐户是否对重要资源具有读/写访问权限？

### <a name="mitigate-potential-security-issues"></a>减少潜在的安全问题

下表显示了本地用户帐户的潜在安全问题和相应的缓解措施。

| 安全问题| 缓解措施 |
| - | - |
| 密码管理|* 确保密码复杂性和密码更改受强大的过程的制约，以确保具有强密码要求的定期更新。 <br> * 使用密码更新协调密码更改以最大程度地减少服务停机时间。 |
| 帐户是特权组的成员。| 查看组成员身份。 删除特权组中的帐户。 只向帐户授予运行服务所需的权限 (咨询服务供应商) 。 例如，你可能能够拒绝本地登录或拒绝交互登录。 |
| 帐户对敏感资源具有读/写访问权限。| 审核对敏感资源的访问。 将审核日志存档到 SIEM (Azure Log Analytics 或 Azure Sentinel) 进行分析。 如果检测到不适当的访问级别，请修正资源权限。 |


## <a name="move-to-more-secure-account-types"></a>转到更安全的帐户类型

Microsoft 不建议客户使用本地用户帐户作为服务帐户。 对于使用此类帐户的任何服务，请评估是否可以将其配置为使用 gMSA 或 sMSA。

此外，评估服务本身是否可以移动到 Azure，以便可以使用更安全的服务帐户类型。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解如何保护服务帐户

* [本地服务帐户简介](service-accounts-on-premises.md)

* [安全组托管服务帐户](service-accounts-group-managed.md)

* [保护独立托管服务帐户](service-accounts-standalone-managed.md)

* [安全计算机帐户](service-accounts-computer.md)

* [保护用户帐户](service-accounts-user-on-premises.md)

* [管理本地服务帐户](service-accounts-govern-on-premises.md)

 
