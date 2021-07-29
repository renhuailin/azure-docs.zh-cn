---
title: 保护计算机帐户 | Azure Active Directory
description: 保护本地计算机帐户安全的帮助指南。
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
ms.openlocfilehash: ec2e8cddb93e2e6fdf9ff804bbc28fd283436131
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108206612"
---
# <a name="secure-on-premises-computer-accounts"></a>保护本地计算机帐户

计算机帐户（或 LocalSystem 帐户）是具有大多数权限的内置帐户，可访问本地计算机上的几乎所有资源。 此帐户不与任何已登录的用户帐户关联。 以 LocalSystem 帐户身份运行的服务通过向远程服务器提供计算机的登录信息（格式为 <domain_name>\\<computer_name>$）以访问网络资源。 计算机帐户的预定义名称为 NT AUTHORITY\SYSTEM。 可以用其启动某个服务，并为该服务提供安全性上下文。

![屏幕截图：计算机帐户上的本地服务列表。](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-a-computer-account"></a>使用计算机帐户的优势

计算机帐户具有以下优势：

* **不受限制的本地访问**：计算机帐户提供对计算机本地资源的完全访问权限。

* 自动化密码管理：无需手动更改密码。 此帐户是 Active Directory 的成员，系统会自动更改帐户密码。 如使用计算机帐户，即可无需为服务注册服务主体名称。

* 有限的计算机外访问权限：Active Directory 域服务 (AD DS) 中的默认访问控制列表允许对计算机帐户进行有限的访问。 如果有未经授权的用户进行访问，则该服务只能访问你网络上的有限资源。

## <a name="assess-the-security-posture-of-computer-accounts"></a>评估计算机帐户的安全状况

下表列出了使用计算机帐户时的一些潜在挑战以及相关缓解措施：
 
| 问题 | 缓解操作 |
| - | - |
| 在计算机离开并重新加入域时，系统会删除并且重新创建计算机帐户。 | 使用本文下一部分提供的示例脚本验证是否需要将计算机添加到 Active Directory 组，并验证已向组中添加哪个计算机帐户。| 
| 如果你将计算机帐户添加到某个组，则在该计算机上以 LocalSystem 帐户身份运行的所有服务都将被授予对该组的访问权限。| 请仔细选择你计算机帐户的组成员身份。 避免使计算机帐户成为任何域管理员组的成员，因为关联服务对 AD DS 具有完全访问权限。 |
| LocalSystem 的网络默认值不正确。 | 不要假设计算机帐户对网络资源具有默认的有限访问权限。 相反，请仔细检查此帐户的组成员身份。 |
| 以 LocalSystem 身份运行的未知服务。 | 确保在 LocalSystem 帐户下运行的所有服务都是 Microsoft 服务或第三方的受信任服务。 |
| | |

## <a name="find-services-that-run-under-the-computer-account"></a>查找在计算机帐户下运行的服务

若要查找在 LocalSystem 上下文下运行的服务，请使用以下 PowerShell cmdlet：

```powershell
Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

若要查找属于特定组成员的计算机帐户，请运行以下 PowerShell cmdlet：

```powershell
Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

若要查找作为标识管理员组的成员的计算机帐户（域管理员、企业管理员和管理员），请运行以下 PowerShell cmdlet：

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```

## <a name="move-from-computer-accounts"></a>从计算机帐户移动

> [!IMPORTANT]
> 计算机帐户是高特权帐户，只应在服务需要对计算机上的本地资源进行无限制访问以及无法使用托管服务帐户 (MSA) 时使用。

* 请与服务所有者核实其服务是否可以使用托管服务帐户运行，并使用组托管服务帐户 (gMSA) 或独立的托管服务帐户 (sMSA)（如果你的服务支持该帐户）。

* 使用只包含运行服务所需权限的域用户帐户。

## <a name="next-steps"></a>后续步骤 

若要详细了解如何保护服务帐户，请参阅以下文章：

* [本地服务帐户简介](service-accounts-on-premises.md)
* [保护组托管服务帐户](service-accounts-group-managed.md)
* [保护独立托管服务帐户](service-accounts-standalone-managed.md)
* [保护用户帐户](service-accounts-user-on-premises.md)  
* [管理本地服务帐户](service-accounts-govern-on-premises.md)
