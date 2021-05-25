---
title: 保护计算机帐户 | Azure Active Directory
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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417051"
---
# <a name="securing-computer-accounts"></a>保护计算机帐户

计算机帐户（或 LocalSystem 帐户）是一个具有高特权的内置帐户，可访问本地计算机上的几乎所有资源。 此帐户不与任何已登录的用户帐户关联。 以 LocalSystem 身份运行的服务通过向远程服务器提供计算机的凭据来访问网络资源。 它以 <domain_name>\<computer_name>$ 形式提供凭据。 计算机帐户的预定义名称是 NT AUTHORITY\SYSTEM。 它可用于启动某个服务并为该服务提供安全性上下文。

![[图 4](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>使用计算机帐户的优势

计算机帐户提供以下优势。

* **不受限制的本地访问**：计算机帐户提供对计算机本地资源的完全访问权限。

* **自动化密码管理**：使用计算机帐户，你不再需要手动更改密码。 相反，此帐户是 Active Directory 的成员，帐户密码会自动更改。 使用它时，也不需要为服务注册服务主体名称。

* **有限的计算机外访问权限**：Active Directory 域服务中的默认访问控制列表 (ACL) 允许计算机帐户进行极少的访问。 如果此服务受到黑客攻击，攻击者只能对你的网络上的资源进行有限的访问。

## <a name="assess-security-posture-of-computer-accounts"></a>评估计算机帐户的安全状况

使用计算机帐户时的潜在挑战和相关的缓解措施。 

| 问题| 缓解措施 |
| - | - |
| 在计算机离开并重新加入域时，计算机帐户会被删除并重新创建。| 使用此页上提供的示例脚本验证是否需要将计算机添加到 AD 组，并验证已将哪个计算机帐户添加到某个组。| 
| 如果你将计算机帐户添加到某个组，则在该计算机上作为 LocalSystem 运行的所有服务都将被授予该组的访问权限。| 请严格选择你的计算机帐户的组成员身份。 避免使计算机帐户成为任何域管理员组的成员，因为关联的服务对 Active Directory 域服务具有完全访问权限。 |
| LocalSystem 的网络默认值不正确| 不要假设计算机帐户对网络资源具有默认的有限访问权限。 相反，请仔细检查此帐户的组成员身份。 |
| 作为 LocalSystem 运行的未知服务| 确保在 LocalSystem 帐户下运行的所有服务都是 Microsoft 服务或第三方的受信任服务。 |


## <a name="find-services-running-under-the-computer-account"></a>查找在计算机帐户下运行的服务

使用以下 PowerShell cmdlet 查找在 LocalSystem 上下文下运行的服务

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**查找是特定组的成员的计算机帐户**

可以使用以下 PowerShell cmdlet 查找是特定组的成员的计算机帐户。

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**查找是特权组的成员的计算机帐户**

可以使用以下 PowerShell cmdlet 查找是标识管理员组（Domain Admins、Enterprise Admins、Administrators）的成员的计算机帐户。

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>从计算机帐户移动

> [!IMPORTANT]
> 计算机帐户是高特权帐户，只应在服务需要对计算机上的本地资源进行无限制访问时使用，并且你不能使用托管服务帐户 (MSA)。

* 请与服务所有者核实其服务是否可以使用 MSA 运行，并使用组托管服务帐户 (gMSA) 或独立的托管服务帐户(sMSA)（如果你的服务支持该帐户）。

* 请使用其权限恰好足够运行你的服务的域用户帐户。

## <a name="next-steps"></a>后续步骤 

参阅以下文章，了解如何保护服务帐户

* [本地服务帐户简介](service-accounts-on-premises.md)

* [保护组托管服务帐户](service-accounts-group-managed.md)

* [保护独立的托管服务帐户](service-accounts-standalone-managed.md)

* [保护计算机帐户](service-accounts-computer.md)

* [保护用户帐户](service-accounts-user-on-premises.md)

* [管理本地服务帐户](service-accounts-govern-on-premises.md)

 

 
