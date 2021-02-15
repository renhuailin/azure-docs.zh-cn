---
title: 保护组托管服务帐户 |Azure Active Directory
description: 有关保护组托管服务帐户计算机帐户的指南。
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
ms.openlocfilehash: 5c73bcd8fb4c6b594633abd1ac268bd8dfd78202
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416977"
---
# <a name="securing-group-managed-service-accounts"></a>保护组托管服务帐户

组托管服务帐户 (Gmsa) 是用于保护服务的托管域帐户。 Gmsa 可以在单个服务器或服务器场中运行，例如网络负载平衡器后面的系统 (NLB) 或 Internet Information Services (IIS) 服务器。 将服务配置为使用 gMSA 主体后，该帐户的密码管理将由 Windows 处理。

## <a name="benefits-of-using-gmsas"></a>使用 Gmsa 的好处

Gmsa 提供了一个具有更高安全性的单一标识解决方案，同时通过以下方式降低了管理开销：

* **设置强密码**。 Gmsa 使用240字节随机生成的复杂密码。 GMSA 密码的复杂性和长度最大程度地降低了服务受到暴力破解或字典攻击的可能性。

* **定期重启密码**。 Gmsa 将密码管理移动到 Windows，每隔30天更改一次密码。 服务和域管理员不再需要计划密码更改或管理服务中断，以确保服务帐户安全。 

* **支持部署到服务器场**。 将 Gmsa 部署到多个服务器的功能允许支持负载均衡解决方案，其中多个主机运行相同的服务。 

* **支持简化的服务器主体名称 (SPN) 管理**。 创建帐户时，可以使用 PowerShell 设置 SPN。 此外，支持自动 SPN 注册的服务可能会针对 gMSA 执行此操作，前提是正确设置了 gMSA 权限。 

## <a name="when-to-use-gmsas"></a>何时使用 Gmsa

使用 Gmsa 作为本地服务的首选帐户类型，除非某个服务（如故障转移群集）不支持该服务。

> [!IMPORTANT]
> 在部署到生产环境之前，必须先通过 Gmsa 测试您的服务。 为此，请设置测试环境，并确保应用程序可以使用 gMSA 并访问它需要访问的资源。 有关详细信息，请参阅 [组托管服务帐户支持](https://docs.microsoft.com/system-center/scom/support-group-managed-service-accounts?view=sc-om-2019)。


如果服务不支持使用 Gmsa，则下一种最佳选择是使用独立的托管服务帐户 (sMSA) 。 sMSAs 提供与 gMSA 相同的功能，但仅用于在单个服务器上部署。

如果你的服务不能使用 gMSA 或 sMSA，则必须将该服务配置为以标准用户帐户的身份运行。 服务和域管理员需要遵守强密码管理过程，以确保帐户安全。

## <a name="assess-the-security-posture-of-gmsas"></a>评估 Gmsa 的安全状况

Gmsa 本质上比标准用户帐户更安全，需要进行密码管理。 但是，在查看总体安全状态时，请考虑 Gmsa 的访问范围。

下表显示了使用 Gmsa 时可能存在的安全问题和缓解措施。

| 安全问题| 缓解措施 |
| - | - |
| gMSA 是特权组的成员。 | 查看组成员身份。 为此，你可以创建一个 PowerShell 脚本来枚举所有组成员身份，然后按 gMSA 文件的名称筛选生成的 CSV 文件。 <br>从特权组中删除 gMSA。<br> 仅授予 gMSA 运行服务所需的权利和权限 (咨询服务供应商) 。 
| gMSA 对敏感资源具有读/写访问权限。 | 审核对敏感资源的访问。 将审核日志存档到 SIEM，例如 Azure Log Analytics 或 Azure Sentinel 进行分析。 如果检测到不适当的访问级别，请删除不必要的资源权限。 |


## <a name="find-gmsas"></a>查找 Gmsa

你的组织可能已创建 Gmsa。 运行以下 PowerShell cmdlet 来检索这些帐户：

若要有效地执行此操作，Gmsa 必须位于托管服务帐户组织单位 (OU) 。

  
![托管服务帐户 OU 的屏幕截图。](./media/securing-service-accounts/secure-gmsa-image-1.png)

若要查找可能不存在的服务 Msa，请参阅以下命令。

**查找所有服务帐户，包括 Gmsa 和 sMSAs：**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount”}
```

## <a name="manage-gmsas"></a>管理 Gmsa

你可以使用以下 Active Directory PowerShell cmdlet 来管理 Gmsa：

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> 从 Windows Server 2012 开始，默认情况下，Uninstall-adserviceaccount cmdlet 适用于 Gmsa。 有关上述 cmdlet 的用法的详细信息，请参阅 [**使用组托管服务帐户入门**](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)。

## <a name="move-to-a-gmsa"></a>移到 gMSA
Gmsa 是用于满足本地需求的最安全的服务帐户类型。 如果可以移动到一个，则应该。 此外，请考虑将服务移到 Azure，将服务帐户移到 Azure Active directory。

1.  确保在 [林中部署 KDS 根密钥](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key)。 这是一次性操作。

2. [创建新的 gMSA](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)。

3. 在运行服务的每个主机上安装新的 gMSA。
   > [!NOTE] 
   > 若要详细了解如何在主机上创建并安装 gMSA，请在将服务配置为使用 gMSA 之前，参阅 [使用组托管服务帐户入门](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))

 
4. 将服务标识更改为 gMSA，并指定空白密码。

5. 验证服务是否在新的 gMSA 标识下运行。

6. 删除旧服务帐户标识。

 

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解如何保护服务帐户

* [本地服务帐户简介](service-accounts-on-premises.md)

* [安全组托管服务帐户](service-accounts-group-managed.md)

* [保护独立托管服务帐户](service-accounts-standalone-managed.md)

* [安全计算机帐户](service-accounts-computer.md)

* [保护用户帐户](service-accounts-user-on-premises.md)

* [管理本地服务帐户](service-accounts-govern-on-premises.md)
