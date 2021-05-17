---
title: 保护独立托管服务帐户 | Azure Active Directory
description: 保护独立托管服务帐户的指南。
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
ms.openlocfilehash: ea3bd3e6fc971901bf69c053088678e8f0f718d0
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108206666"
---
# <a name="secure-standalone-managed-service-accounts"></a>保护独立托管服务帐户

独立托管服务帐户 (sMSA) 是用于帮助保护服务器上运行的一个或多个服务的托管域帐户。 它们不能在多个服务器之间重复使用。 sMSA 提供自动密码管理、简化的服务主体名称 (SPN) 管理以及将管理权委托给其他管理员的功能。 

在 Active Directory 中，sMSA 与运行服务的特定服务器相关联。 你可以在 Microsoft 管理控制台的“Active Directory 用户和计算机”管理单元中找到这些列出的帐户。

![“Active Directory 用户和计算机”管理单元的屏幕截图，其中显示了托管服务帐户 OU。](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

托管服务帐户是随 Windows Server 2008 R2 Active Directory 架构引入的，因此至少需要 Windows Server 2008 R2。 

## <a name="benefits-of-using-smsas"></a>使用 sMSA 的好处

与用作服务帐户的用户帐户相比，sMSA 提供了更高的安全性。 同时通过以下方式帮助降低管理开销：

* **设置强密码**：sMSA 使用随机生成的 240 字节的复杂密码。 sMSA 密码的复杂性和长度最大程度地降低了服务受到暴力破解攻击或字典攻击的可能性。

* **定期轮换密码**：Windows 每 30 天自动更改一次 sMSA 密码。 服务和域管理员无需计划密码更改或管理相关的停机时间。

* **简化 SPN 管理**：如果域功能级别为 Windows Server 2008 R2，则服务主体名称会自动更新。 例如，在以下情况下，服务主体名称会自动更新：
   * 重命名主计算机帐户。  
   * 更改主计算机的域名服务器 (DNS) 名称。  
   * 使用 [PowerShell](/powershell/module/activedirectory/set-adserviceaccount) 添加或删除其他 sam-accountname 或 dns-hostname 参数。

## <a name="when-to-use-smsas"></a>何时使用 sMSA

sMSA 可简化管理和安全任务。 将一个或多个服务部署到单个服务器，并且不能使用组托管服务帐户 (gMSA) 时，请使用 sMSA。 

> [!NOTE] 
> 虽然可以将 sMSA 用于多个服务，但建议你让每个服务都使用自己的标识进行审核。 

如果软件的创建者无法告诉你它是否可以使用 MSA，你必须测试自己的应用程序。 为此，请创建一个测试环境，并确保它可以访问所有必需的资源。 有关详细信息，请参阅[创建和安装 sMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting)。

### <a name="assess-the-security-posture-of-smsas"></a>评估 sMSA 的安全状况

sMSA 本质上比需要持续进行密码管理的标准用户帐户更安全。 但是，请务必将 sMSA 的访问范围视为其整体安全态势的一部分。

若要查看如何缓解 sMSA 造成的潜在安全问题，请参阅下表：

| 安全问题| 缓解措施 |
| - | - |
| sMSA 是特权组的成员。 | <li>从提升权限的特权组（例如“域管理员”组）中删除 sMSA。<li>使用最少特权模型，为 sMSA 仅授予运行其服务所需的权利和权限。<li>如果你不确定所需的权限，请咨询服务创建者。 |
| sMSA 具有对敏感资源的读/写访问权限。 | <li>审核对敏感资源的访问。<li>将审核日志存档到安全信息和事件管理 (SIEM) 计划（如 Azure Log Analytics 或 Azure Sentinel）进行分析。<li>如果检测到不适当的访问级别，请修正资源权限。 |
| 默认情况下，sMSA 密码滚动更新频率为 30 天。 | 根据企业安全要求，可以使用组策略来优化持续时间。 若要设置密码过期期限，请使用以下路径：<br>Computer Configuration\Policies\Windows Settings\Security Settings\Security Options。 对于域成员，请使用“计算机帐户密码最长期限”。 |
| | |



### <a name="challenges-with-smsas"></a>sMSA 的挑战

与 sMSA 关联的挑战如下所示：

| 挑战| 缓解措施 |
| - | - |
| sMSA 只能在单个服务器上使用。 | 如果需要跨服务器使用帐户，请使用 gMSA。 |
| sMSA 不能跨域使用。 | 如果需要跨域使用帐户，请使用 gMSA。 |
| 并非所有应用程序都支持 sMSA。 | 如果可能，请使用 gMSA。 否则，请使用应用程序创建者建议的标准用户帐户或计算机帐户。 |
| | |


## <a name="find-smsas"></a>查找 sMSA

在任何域控制器上，运行 DSA.msc 并展开托管服务帐户容器以查看所有 sMSA。 

若要返回 Active Directory 域中的所有 sMSA 和 gMSA，请运行以下 PowerShell 命令： 

`Get-ADServiceAccount -Filter *`

若要仅返回 Active Directory 域中的 sMSA，请运行以下命令：

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>管理 sMSA

若要管理 sMSA，可以使用以下 Active Directory PowerShell cmdlet：

`Get-ADServiceAccount`  
` Install-ADServiceAccount`  
` New-ADServiceAccount`  
` Remove-ADServiceAccount`  
`Set-ADServiceAccount`  
`Test-ADServiceAccount`  
`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>移到 sMSA

如果应用程序服务支持 sMSA 而不是 gMSA，并且当前正在将用户帐户或计算机帐户用于安全上下文，请在服务器上[创建并安装 sMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting)。 

理想情况下，可将资源移到 Azure，并使用 Azure 托管标识或服务主体。

## <a name="next-steps"></a>后续步骤

若要详细了解如何保护服务帐户，请参阅以下文章：

* [本地服务帐户简介](service-accounts-on-premises.md)  
* [保护组托管服务帐户](service-accounts-group-managed.md)  
* [保护计算机帐户](service-accounts-computer.md)  
* [保护用户帐户](service-accounts-user-on-premises.md)  
* [管理本地服务帐户](service-accounts-govern-on-premises.md)
