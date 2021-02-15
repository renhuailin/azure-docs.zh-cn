---
title: 保护计算机帐户 |Azure Active Directory
description: 有关保护本地计算机帐户的指南。
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
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417051"
---
# <a name="securing-computer-accounts"></a>保护计算机帐户

计算机帐户（或 LocalSystem 帐户）是一个具有高特权的内置帐户，可访问本地计算机上的几乎所有资源。 此帐户未与任何已登录的用户帐户关联。 以 LocalSystem 身份运行的服务通过向远程服务器提供计算机的凭据来访问网络资源。 它以 <domain_name>$ 形式提供凭据 \<computer_name> 。 计算机帐户的预定义名称为 NT AUTHORITY\SYSTEM。 它可用于启动服务并提供该服务的安全上下文。

![[Picture 4] (.\media\securing-service-accounts\secure-computer-accounts-image-1.png) ](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>使用计算机帐户的优点

计算机帐户提供以下优势。

* 不 **受限制的本地访问**：计算机帐户提供对计算机本地资源的完全访问权限。

* **自动密码管理**：计算机帐户不再需要手动更改密码。 相反，此帐户是 Active Directory 的成员，帐户密码会自动更改。 它还无需为服务注册服务主体名称。

* **计算机外的有限访问权限**：默认访问控制列表 (ACL) 在 Active Directory 域服务允许对计算机帐户进行最小限度的访问。 如果此服务受到黑客攻击，则只能访问网络上的资源。

## <a name="assess-security-posture-of-computer-accounts"></a>评估计算机帐户的安全状况

使用计算机帐户时的潜在挑战和关联的缓解措施。 

| 问题| 缓解措施 |
| - | - |
| 计算机在计算机离开并重新加入域时，计算机帐户会被删除和重新使用。| 验证是否需要将计算机添加到 AD 组，并使用此页上提供的示例脚本验证是否已将计算机帐户添加到组中。| 
| 如果将计算机帐户添加到组中，则在该计算机上作为 LocalSystem 运行的所有服务都将被授予该组的访问权限。| 选择计算机帐户的组成员身份。 避免使计算机帐户成为任何域管理员组的成员，因为关联服务对 Active Directory 域服务具有完全访问权限。 |
| LocalSystem 的网络默认值不正确| 不要假设计算机帐户对网络资源具有默认的有限访问权限。 相反，请仔细检查此帐户的组成员身份。 |
| 作为 LocalSystem 运行的未知服务| 确保在 LocalSystem 帐户下运行的所有服务都是第三方的 Microsoft 服务或受信任的服务。 |


## <a name="find-services-running-under-the-computer-account"></a>查找在计算机帐户下运行的服务

使用以下 PowerShell cmdlet 查找在 LocalSystem 上下文下运行的服务

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**查找属于特定组成员的计算机帐户**

使用以下 PowerShell cmdlet 查找属于特定组的计算机帐户。

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**查找作为特权组成员的计算机帐户**

使用以下 PowerShell cmdlet 查找作为身份管理员组成员的计算机帐户 (Domain Admins、Enterprise Admins、Administrators) 

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>从计算机帐户移动

> [!IMPORTANT]
> 计算机帐户是特权较高的帐户，只应在服务需要对计算机上本地资源的无限制访问权限时使用，而不能使用托管服务帐户 (MSA) 。

* 如果服务所有者的服务可以使用 MSA 运行，请咨询你的服务所有者，如果服务支持，请使用组托管服务帐户 (gMSA) 或独立托管服务帐户 (sMSA) 。

* 使用域用户帐户，只包含运行服务所需的权限。

## <a name="next-steps"></a>后续步骤 

请参阅以下文章，了解如何保护服务帐户

* [本地服务帐户简介](service-accounts-on-premises.md)

* [安全组托管服务帐户](service-accounts-group-managed.md)

* [保护独立托管服务帐户](service-accounts-standalone-managed.md)

* [安全计算机帐户](service-accounts-computer.md)

* [保护用户帐户](service-accounts-user-on-premises.md)

* [管理本地服务帐户](service-accounts-govern-on-premises.md)

 

 
