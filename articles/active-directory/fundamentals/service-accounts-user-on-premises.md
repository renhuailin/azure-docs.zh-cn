---
title: 保护基于用户的服务帐户 | Azure Active Directory
description: 保护基于用户的服务帐户的指南。
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
ms.openlocfilehash: 79e0dc10aa9cb5fb67812cca31d2cd892afcccbe
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108208088"
---
# <a name="secure-user-based-service-accounts-in-active-directory"></a>在 Active Directory 中保护基于用户的服务帐户

使用本地用户帐户是帮助保护在 Windows 上运行的服务的传统方法。 这些帐户仅在必要时使用，就是当你的服务既不支持组托管服务帐户 (gMSA) 也不支持独立托管服务帐户 (sMSA) 的时候。 有关选择要使用的最佳帐户类型的信息，请参阅[本地服务帐户简介](service-accounts-on-premises.md)。 

你可能还需要调查是否可以迁移你的服务，以使用 Azure 服务帐户（例如托管标识或服务主体）。 

你可以创建本地用户帐户，为帐户提供访问本地和网络资源所需的服务和权限的安全上下文。 与其他任何 Active Directory 用户帐户一样，本地用户帐户需要手动管理密码。 服务和域管理员需要遵守强密码管理流程，以帮助确保这些帐户的安全。

创建用作服务帐户的用户帐户时，仅将其用于单个服务。 命名时，要清楚地表明该帐户是一个服务帐户以及用于哪项服务。 

## <a name="benefits-and-challenges"></a>优点和挑战

本地用户帐户可以提供显著的优势。 这些帐户是用于服务的最通用的帐户类型。 用于监管普通用户帐户的所有策略均可控制用作服务帐户的用户帐户。 但应仅在无法使用 MSA 时才使用它们。 还应评估计算机帐户是否是更好的选择。 

下表总结了与使用本地用户帐户关联的挑战：

| 挑战 | 缓解措施 |
| - | - |
| 密码管理是一个手动过程，可能会导致安全性降低和服务故障。| <li>确保可通过可靠流程管控密码复杂性和密码更改，该流程应确保使用强密码进行定期更新。<li>在服务上使用密码更新来协调密码更改，这有助于减少服务故障时间。 |
| 识别充当服务帐户的本地用户帐户可能会很困难。 | <li>记录并维护在你的环境中部署的服务帐户的相关记录。<li>跟踪帐户名和向这些帐户分配了访问权限的资源。<li>考虑将“svc-”前缀添加到用作服务帐户的所有用户帐户。 |
| | |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>查找用作服务帐户的本地用户帐户

本地用户帐户与任何其他 Active Directory 用户帐户一样。 因此，查找这些帐户可能会很困难，因为用户帐户没有可将自身标识为服务帐户的单独属性。 

建议为用作服务帐户的任何用户帐户创建一个易于识别的命名约定。 例如，可以添加“svc-”作为前缀，并将服务命名为“svc-HRDataConnector”。

你可以使用以下某些条件来查找这些服务帐户。 但是，这种方法可能找不到所有帐户，例如：

* 受信任的委派帐户。  
* 具有服务主体名称的帐户。  
* 密码设置为永不过期的帐户。

若要查找为服务创建的本地用户帐户，可以运行以下 PowerShell 命令。

若要查找受信任的委派帐户：

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

若要查找具有服务主体名称的帐户：

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

若要查找密码设置为永不过期的帐户：

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```

还可审核对敏感资源的访问，并将审核日志存档到安全信息和事件管理 (SIEM) 系统。 使用诸如 Azure Log Analytics 或 Azure Sentinel 等系统，可搜索并分析服务帐户。

## <a name="assess-the-security-of-on-premises-user-accounts"></a>评估本地用户帐户的安全性

可使用以下条件评估用作服务帐户的本地用户帐户的安全性：

* 什么是密码管理策略？  
* 帐户是否为任何特权组的成员？  
* 帐户是否具有重要资源的读/写权限？

### <a name="mitigate-potential-security-issues"></a>缓解潜在的安全问题

下表总结了本地用户帐户的潜在安全问题及其缓解措施：

| 安全问题 | 缓解措施 |
| - | - |
| 密码管理。| <li>确保可通过可靠的流程管控密码复杂性和密码更改，该流程应包括定期更新和严格的密码要求。<li>使用密码更新来协调密码更改，以最大限度减少服务故障时间。 |
| 帐户是特权组的成员。| <li>评审组成员身份。<li>将帐户从特权组中删除。<li>仅向帐户授予运行服务所需的权限（咨询服务供应商）。 例如，你可以拒绝本地登录或拒绝交互式登录。 |
| 该帐户具有敏感资源的读/写权限。| <li>审核对敏感资源的访问。<li>将审核日志存档到 SIEM（Azure Log Analytics 或 Azure Sentinel）进行分析。<li>如果检测到不需要的访问级别，请修正资源权限。 |
| | |


## <a name="move-to-more-secure-account-types"></a>转为使用更安全的帐户类型

Microsoft 不建议将本地用户帐户用作服务帐户。 对于使用此类帐户的任何服务，请评估可将服务配置为使用 gMSA 还是 sMSA。

此外，评估服务本身是否可以迁移至 Azure，以使用更安全的服务帐户类型。 

## <a name="next-steps"></a>后续步骤

若要详细了解如何保护服务帐户，请参阅以下文章：

* [本地服务帐户简介](service-accounts-on-premises.md)  
* [保护组托管服务帐户](service-accounts-group-managed.md)  
* [保护独立托管服务帐户](service-accounts-standalone-managed.md)  
* [保护计算机帐户](service-accounts-computer.md)  
* [管理本地服务帐户](service-accounts-govern-on-premises.md)

 
