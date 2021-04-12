---
title: Active Directory 服务帐户简介 | Azure Active Directory
description: 介绍 Active Directory 中的服务帐户类型，以及如何为其提供保护。
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
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645610"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Active Directory 服务帐户简介

服务具有主要安全标识，该标识决定本地和网络资源的访问权限。 Microsoft Win32 服务的安全性上下文由用于启动该服务的服务帐户确定。 服务帐户用于：
* 标识并验证服务
* 成功启动服务
* 访问或执行代码或应用程序
* 启动进程。 

## <a name="types-of-on-premises-service-accounts"></a>本地服务帐户的类型

根据用例，你可以使用托管服务帐户 (MSA)、计算机帐户或用户帐户来运行服务。 必须测试服务，以确认他们可以使用托管服务帐户。 如果可以，应使用托管服务帐户。

### <a name="group-msa-accounts"></a>组 MSA 帐户

对于在本地环境中运行的服务，请尽可能使用[组托管服务帐户](service-accounts-group-managed.md) (gMSA)。 gMSA 为在服务器场或网络负载均衡器后面运行的服务提供了单一标识解决方案。 它们还可用于在的单一服务器上运行的服务。 [gMSA 具有必须满足的特定要求](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>独立 MSA 帐户

如果无法使用 gMSA，请使用[独立托管服务帐户](service-accounts-standalone-managed.md) (sMSA)。 sMSA 至少需要 Windows Server 2008R2。 与 gMSA 不同，sMSA 只能在一个服务器上运行。 它们可用于该服务器上的多个服务。

### <a name="computer-account"></a>计算机帐户

如果无法使用 MSA，请使用[计算机帐户](service-accounts-computer.md)进行调查。 LocalSystem 帐户是预定义的本地帐户，该帐户在本地计算机上具有广泛的权限，并充当网络上的计算机标识。   
以 LocalSystem 帐户身份运行的服务通过使用计算机帐户的凭据（格式为 <domain_name>\<computer_name>）访问网络资源。

NT AUTHORITY\SYSTEM 是 LocalSystem 帐户的预定义名称。 它可用于启动某个服务并为该服务提供安全性上下文。

> [!NOTE]
> 使用计算机帐户时，无法判断计算机上的哪个服务正在使用该帐户，因此无法审核正在进行更改的服务。 

### <a name="user-account"></a>用户帐户

如果无法使用 MSA，请使用[用户帐户](service-accounts-user-on-premises.md)进行调查。 用户帐户可以是域用户帐户或本地用户帐户。

域用户帐户支持服务充分利用 Windows 和 Microsoft Active Directory 域服务的服务安全功能。 该服务将向帐户授予本地和网络访问权限。 它还将拥有该帐户所属的任何组的权限。 域服务帐户支持 Kerberos 相互身份验证。

本地用户帐户（名称格式：“.\UserName”）仅存在于主计算机的 SAM 数据库中，它在 Active Directory 域服务中没有用户对象。 域不能对本地帐户进行身份验证。 因此，在本地用户帐户的安全性上下文中运行的服务不能访问网络资源（匿名用户除外）。 在本地用户上下文中运行的服务不支持 Kerberos 相互身份验证，即提供服务的客户端对服务进行身份验证。 出于上述原因，本地用户帐户通常不适用于启用目录的服务。

> [!IMPORTANT]
> 服务帐户不应是任何特权组的成员，因为特权组成员身份会授予可能带来安全风险的权限。 出于审核和安全目的，每个服务都应有自己的服务帐户。

## <a name="choose-the-right-type-of-service-account"></a>选择正确的服务帐户类型


| 条件| gMSA| sMSA| 计算机帐户| 用户帐户 |
| - | - | - | - | - |
| 应用在单一服务器上运行| 是| 是。 如果可能，请使用 gMSA| 是的。 如果可能，请使用 MSA| 是的。 如果可能，请使用 MSA。 |
| 应用在多个服务器上运行| 是| 否| 不能。 帐户与服务器相关联| 是的。 如果可能，请使用 MSA。 |
| 应用在负载均衡器后运行| 是| 否| 否| 可以。 仅在不能使用 gMSA 时使用 |
| 应用在 Windows Server 2008 R2 上运行| 否| 是| 是。 如果可能，请使用 MSA。| 是的。 如果可能，请使用 MSA。 |
| 在 Windows Server 2012 上运行| 是| 是。 如果可能，请使用 gMSA| 是的。 如果可能，请使用 MSA| 是的。 如果可能，请使用 MSA。 |
| 要求将服务帐户限制为单一服务器| 否| 是| 是。 如果可能，请使用 sMSA| 不能。 |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>使用服务器日志和 PowerShell 进行调查

可以使用服务器日志来确定运行应用程序的服务器及其数量。

可以运行以下 PowerShell 命令，获取网络中所有服务器的 Windows Server 版本的列表。 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>查找本地服务帐户

建议将前缀（如“svc”）添加 到用作服务帐户的所有帐户。 此命名约定使它们更易于查找和管理。 还应考虑对服务帐户和服务帐户的所有者使用说明属性，这可以是团队别名或安全团队所有者。

查找本地服务帐户是确保其安全性的关键所在。 而且，对于非 MSA 帐户而言，这可能很困难。 建议审查有权访问你的重要本地资源的所有帐户，并确定哪些计算机或用户帐户可以作为服务帐户。 还可以使用以下方法来查找帐户。

* 每种类型的帐户的相关文章都提供查找该帐户类型的详细步骤。 有关这些文章的链接，请参阅本文的“后续步骤”部分。

## <a name="document-service-accounts"></a>文档服务帐户

在本地环境中找到服务帐户后，请记录每个帐户的以下信息。 

* 所有者。 负责维护帐户的人员。

* 用途。 帐户表示的应用程序或其他用途。 

* 权限范围。 它具有什么权限，它应该具有哪些权限？ 如果它是某个组的成员，该怎么办？

* 风险状况。 如果该帐户泄露，对你的业务有什么风险？ 如果风险很高，请使用 MSA。

* 预期的生存期和定期证明。 你预计该帐户可以使用多长时间？ 所有者必须多久对持续的需求进行一次审查和证明？

* 密码安全性。 适用于存储密码的用户和本地计算机帐户。 确保密码安全，并记录谁有权访问。 请考虑使用 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 来保护存储的密码。 

  

## <a name="next-steps"></a>后续步骤

参阅以下文章，了解如何保护服务帐户

* [本地服务帐户简介](service-accounts-on-premises.md)

* [保护组托管服务帐户](service-accounts-group-managed.md)

* [保护独立托管服务帐户](service-accounts-standalone-managed.md)

* [保护计算机帐户](service-accounts-computer.md)

* [保护用户帐户](service-accounts-user-on-premises.md)

* [管理本地服务帐户](service-accounts-govern-on-premises.md)

