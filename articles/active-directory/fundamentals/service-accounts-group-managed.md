---
title: 保护组托管服务帐户 | Azure Active Directory
description: 有关保护组托管服务帐户 (gMSA) 计算机帐户的指南。
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
ms.openlocfilehash: 6b823fbcc124a67980fcd1f0440ae107de01fc20
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114690397"
---
# <a name="secure-group-managed-service-accounts"></a>保护组托管服务帐户

组托管服务帐户 (gMSA) 是用来帮助保护服务的托管域帐户。 gMSA 可以在单个服务器上运行，也可以在服务器场中运行，例如支持网络负载均衡的系统或 Internet Information Services (IIS) 服务器。 将服务配置为使用 gMSA 主体后，该帐户的密码管理将由 Windows 操作系统处理。

## <a name="benefits-of-using-gmsas"></a>使用 gMSA 的好处

gMSA 提供了具有更高安全性的单个标识解决方案。 同时通过以下方式帮助降低管理开销：

* 设置强密码：gMSA 使用随机生成的 240 字节复杂密码。 gMSA 密码的复杂性和长度最大程度地降低了服务受到暴力破解或字典攻击的可能性。

* 定期循环更改密码：gMSA 将密码管理工作移交给 Windows 操作系统，后者每 30 天更改一次密码。 服务和域管理员不再需要计划密码更改或管理服务中断，以帮助确保服务帐户安全。 

* 支持部署到服务器场：通过允许将 gMSA 部署到多个服务器，可支持多个主机运行同一服务的负载均衡解决方案。 

* 支持简化的服务主体名称 (SPN) 管理：创建帐户时，可以使用 PowerShell 设置 SPN。 另外，如果正确设置了 gMSA 权限，支持自动 SPN 注册的服务可能会对 gMSA 执行此操作。 

## <a name="when-to-use-gmsas"></a>何时使用 gMSA

将 gMSA 用作本地服务的首选帐户类型，除非服务（比如故障转移群集）不支持它。

> [!IMPORTANT]
> 将服务部署到生产环境之前，必须先使用 gMSA 对其进行测试。 为此，请设置测试环境，确保应用程序可以使用 gMSA，并且可以访问需要访问的资源。 有关详细信息，请参阅[对组托管服务帐户的支持](/system-center/scom/support-group-managed-service-accounts)。


如果某项服务不支持使用 gMSA，你的下一个最佳选择是使用独立的托管服务帐户 (sMSA)。 sMSA 提供与 gMSA 相同的功能，但仅用于单个服务器上的部署。

如果你不能使用你的服务支持的 gMSA 或 sMSA，则必须将此服务配置为以标准用户帐户的身份运行。 服务和域管理员必须遵守强密码管理流程，以帮助确保帐户安全。

## <a name="assess-the-security-posture-of-gmsas"></a>评估 gMSA 的安全状况

gMSA 帐户本质上比需要持续进行密码管理的标准用户帐户更安全。 但是，在检查 gMSA 的整体安全状况时，必须考虑 gMSA 的访问范围。

下表显示了使用 gMSA 的潜在安全问题和缓解措施：

| 安全问题| 缓解措施 |
| - | - |
| gMSA 是特权组的成员。 | <li>评审组成员身份。 为此，请创建 PowerShell 脚本以枚举所有组成员身份。 然后，你可以按 gMSA 文件的名称筛选生成的 CSV 文件。<li>从特权组中删除 gMSA。<li>仅向 gMSA 授予运行服务所需的权限（咨询服务供应商）。 
| gMSA 对敏感资源具有读/写访问权限。 | <li>审核对敏感资源的访问。<li>将审核日志存档到 SIEM（例如 Azure Log Analytics 或 Azure Sentinel）以进行分析。<li>如果检测到不适当级别的访问，请删除不必要的资源权限。 |
| | |


## <a name="find-gmsas"></a>查找 gMSA

你的组织可能已经创建了 gMSA。 若要检索这些帐户，请运行以下 PowerShell cmdlet：

```powershell
Get-ADServiceAccount 
Install-ADServiceAccount 
New-ADServiceAccount 
Remove-ADServiceAccount 
Set-ADServiceAccount 
Test-ADServiceAccount 
Uninstall-ADServiceAccount
```


为了有效地执行此操作，gMSA 必须位于托管服务帐户组织单位 (OU) 中。

  
![托管服务帐户 OU 中的 gMSA 帐户的屏幕截图。](./media/securing-service-accounts/secure-gmsa-image-1.png)

若要查找可能不在列表中的服务 MSA，请运行以下命令：

```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all managed service accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where-object {$_.ObjectClass -eq "msDS-GroupManagedServiceAccount"}
```

## <a name="manage-gmsas"></a>管理 gMSA

可以使用以下 Active Directory PowerShell cmdlet 来管理 gMSA 帐户：

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> 从 Windows Server 2012 开始，*-ADServiceAccount cmdlet 默认用于 gMSA。 有关使用上述 cmdlet 的详细信息，请参阅[组托管服务帐户入门](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)。

## <a name="move-to-a-gmsa"></a>迁移到 gMSA
gMSA 帐户是可以满足本地需求的最安全的服务帐户类型。 如果可以迁移到 gMSA，就应该迁移。 此外，请考虑将服务迁移到 Azure，将服务帐户迁移到 Azure Active Directory。 若要迁移到 gMSA 帐户，请执行以下操作：

1. 确保在林中部署[密钥分发服务 (KDS) 根密钥](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key)。 这是一次性操作。

1. [创建一个新的 gMSA](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)。

1. 在运行此服务的每个主机上安装新的 gMSA。
   > [!NOTE] 
   > 有关在主机上创建和安装 gMSA 的详细信息，请在将服务配置为使用 gMSA 之前，参阅[组托管服务帐户入门](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))。

1. 将服务标识更改为 gMSA，并指定一个空密码。

1. 验证服务是否在新的 gMSA 标识下运行。

1. 删除旧的服务帐户标识。

 

## <a name="next-steps"></a>后续步骤

若要详细了解如何保护服务帐户，请参阅以下文章：

* [本地服务帐户简介](service-accounts-on-premises.md)  
* [保护独立托管服务帐户](service-accounts-standalone-managed.md)  
* [保护计算机帐户](service-accounts-computer.md)  
* [保护用户帐户](service-accounts-user-on-premises.md)  
* [管理本地服务帐户](service-accounts-govern-on-premises.md)
