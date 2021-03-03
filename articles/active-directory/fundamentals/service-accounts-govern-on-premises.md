---
title: 管理本地服务帐户 |Azure Active Directory
description: 用于创建和运行服务帐户的帐户生命周期过程的指南
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
ms.openlocfilehash: 88fdfa1f449a0b65861ee09f2e78055a606c99d3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649200"
---
# <a name="governing-on-premises-service-accounts"></a>管理本地服务帐户

Windows Active Directory 中有四种类型的本地服务帐户：

* [组托管服务帐户](service-accounts-group-managed.md) (gmsa) 

* [独立托管服务帐户](service-accounts-standalone-managed.md) (sMSAs) 

* [计算机帐户](service-accounts-computer.md)

* [用作服务帐户的用户帐户](service-accounts-user-on-premises.md)


严格控制服务帐户至关重要： 

* 根据服务帐户的使用情况要求和用途保护它们。

* 管理服务帐户及其凭据的生命周期。

* 根据服务帐户的公开风险和其携带的权限，对其进行评估。 

* 确保 Active Directory 和 Azure Active Directory 没有任何过时的服务帐户，并且可能会产生更大的权限。

## <a name="principles-for-creating-a-new-service-account"></a>用于创建新服务帐户的原则

创建新的服务帐户时，请使用以下准则。

| 原则| 注意事项 | 
| - |- | 
| 服务帐户映射| 将服务帐户关联到单个服务、应用程序或脚本。 |
| 所有权| 确保有请求并承担帐户责任的所有者。 |
| 范围| 明确定义作用域，并预测服务帐户的使用期限。 |
| 目的| 为单一特定目的创建服务帐户。 |
| Privilege| 通过以下方式应用最低权限原则： <br>不要将其分配给内置组（如管理员）。<br> 删除本地计算机权限（如果适用）。<br>为目录访问定制访问和使用 Active Directory 委托。<br>使用粒度访问权限。<br>设置基于用户的服务帐户的帐户过期和基于位置的限制 |
| 监视和审核使用情况| 监视登录数据，并确保其与预期用途匹配。 设置异常使用情况的警报。 |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>为用户帐户强制执行最低权限，并限制帐户滥用

将以下设置用于用作服务帐户的用户帐户：

* [**帐户过期**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)：将服务帐户设置为在其审阅期后自动过期设置的时间，除非确定它应该继续

*  **LogonWorkstations**：限制服务帐户可登录的权限。 如果它在计算机上本地运行并仅访问该计算机上的资源，则将其限制为在其他任何位置进行登录。

* [**无法更改密码**](/powershell/module/addsadministration/set-aduser?view=win10-ps)：通过将参数设置为 false，禁止服务帐户更改其密码。

 
## <a name="build-a-lifecycle-management-process"></a>构建生命周期管理过程

若要维护你的服务帐户的安全性，你必须从确定需要时管理它们，直到它们已停止。 

使用以下过程来管理服务帐户的生命周期：

1. 收集帐户的使用情况信息
1. 将服务帐户和应用程序载入配置管理数据库 (CMDB) 
1. 执行风险评估或正式审查
1. 创建服务帐户并应用限制。
1. 计划和执行定期评审。 根据需要调整权限和作用域。
1. 适当时取消设置帐户。

### <a name="collect-usage-information-for-the-service-account"></a>收集服务帐户的使用情况信息

收集每个服务帐户的相关业务信息。 下表显示了要收集的最小信息，但您应收集为帐户存在做出业务案例所需的一切。

| 数据| 详细信息 |
| - | - |
| “所有者”| 负责服务帐户的用户或组 |
| 目的| 服务帐户的用途 |
| 权限 (范围) | 预期权限集 |
| 配置管理数据库 (CMDB) 链接| 跨链接服务帐户与目标脚本/应用程序和所有者 (s)  |
| 风险| 基于安全风险评估的风险和业务影响评分 |
| 生存期| 启用帐户过期或重新认证计划的预期最大生存期 |


 

理想情况下，为帐户自助服务发出请求，并要求提供相关信息。 所有者，可以是应用程序或企业所有者、IT 成员或基础结构所有者。 如果帐户已获批准，则使用如 Microsoft forms 提供此请求和相关信息的工具可以轻松地将其移植到 CMDB 清单工具。

### <a name="onboard-service-account-to-cmdb"></a>将服务帐户加入到 CMDB

将收集的信息存储在 CMDB 类型的应用程序中。 除了业务信息外，还包括其他基础结构、应用和进程的所有依赖项。  此中央存储库可简化以下操作：

* 评估风险。

* 将服务帐户配置为具有所需的限制。

* 了解相关的功能和安全依赖关系。

* 进行定期检查，以获得安全性和持续需要。

* 请联系所有者 () ，查看、停用和更改服务帐户。

请考虑使用一个服务帐户，该帐户用于运行网站并具有连接到一个或多个 SQL 数据库的权限。 为此服务帐户存储在 CMDB 中的信息可能是：

|数据 | 详细信息|
| - | - |
| 所有者、Deputy| John 布隆，Anna Mayers |
| 目的| 运行 HR 网页并连接到 HR 数据库。 在访问数据库时，可以模拟最终用户。 |
| 权限，范围| HR-Web 服务器：本地登录，运行网页<br>HR-SQL1：本地登录，读取所有 HR * 数据库<br>HR-SQL2：本地登录，对薪金 * 数据库进行读取 |
| Cost Center| 883944 |
| 风险评估| Medium业务影响：中型;私人信息;Medium |
| 帐户限制| 登录到：仅限前面提到的服务器;无法更改密码;MBI-Password 策略; |
| 生存期| l |
| 查看周期| 按所有者，按安全团队 (按隐私)  |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>执行服务帐户使用情况的风险评估或正式审查

考虑到其权限和用途，请评估该帐户在其关联的应用程序或服务中可能带来的风险，以及在该帐户受到威胁的情况下的基础结构。 同时考虑直接和间接的风险。 

* 攻击者可以直接访问哪些内容？

* 服务帐户可以访问的其他信息或系统有哪些？

* 帐户是否可以用于授予其他权限？

* 如何知道权限更改的时间？

完成并记录风险评估后，可能会对此产生影响：

* 帐户限制

* 帐户生存期

* 帐户审阅要求 (节奏和审阅者) 

### <a name="create-a-service-account-and-apply-account-restrictions"></a>创建服务帐户并应用帐户限制

仅在 CMDB 中记录相关信息并执行风险评估后，才创建服务帐户。 应将帐户限制与风险评估相一致。 与评估相关时，请考虑以下限制：

* [帐户到期日期](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)

   * 对于用作服务帐户的所有用户帐户，请定义实际使用的、明确的结束日期。 使用 "帐户过期" 标志设置此设置。 有关更多详细信息，请参阅[ ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration?view=win10-ps)。 

* 登录到 ([LogonWorkstation](/powershell/module/addsadministration/set-aduser?view=win10-ps)) 

* [密码策略](../../active-directory-domain-services/password-policy.md) 要求

* 在 [OU 位置](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) 创建，可确保仅为特权用户进行管理

* 设置和收集审核 [，以检测](/windows/security/threat-protection/auditing/audit-directory-service-changes) 对服务帐户和 [服务帐户](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html)的更改。

准备好投入生产时，请安全地向服务帐户授予访问权限。 

### <a name="schedule-regular-reviews-of-service-accounts"></a>计划服务帐户的定期审查

设置归类为中等和高风险的服务帐户的定期审查。 评论应包括： 

* 所有者证明，是对帐户的持续需求，以及特权和作用域的理由。

* 按隐私和安全团队进行评审，包括对上游和下游连接进行评估。

* 来自审核的数据确保它仅用于预期目的

### <a name="deprovision-service-accounts"></a>取消预配服务帐户

在取消预配过程中，首先删除权限和监视，并在适当的情况下删除帐户。

在以下情况中取消设置服务帐户：

* 为其创建服务帐户的脚本或应用程序已停用。

* 脚本或应用程序中的函数（服务帐户用于 (）例如，已停用对特定资源) 的访问。

* 服务帐户已替换为不同的服务帐户。

删除所有权限后，使用此过程来删除帐户。

1. 取消预配关联的应用程序或脚本后，监视关联的服务 (帐户的登录和资源访问) ，以确保它不在其他进程中使用。 如果你确定不再需要它，请继续下一步。

2. 禁用服务帐户登录，并确保不再需要该帐户。 为时间帐户创建业务策略应保持禁用状态。

3. 请在完成保留禁用策略后删除服务帐户。 

   * 对于 Msa，你可以使用 PowerShell [将其卸载](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps) 或从托管服务帐户容器中手动删除。

   * 对于计算机或用户帐户，您可以从 Active Directory 中手动删除该帐户。

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解如何保护服务帐户

* [本地服务帐户简介](service-accounts-on-premises.md)

* [安全组托管服务帐户](service-accounts-group-managed.md)

* [保护独立托管服务帐户](service-accounts-standalone-managed.md)

* [安全计算机帐户](service-accounts-computer.md)

* [保护用户帐户](service-accounts-user-on-premises.md)

* [管理本地服务帐户](service-accounts-govern-on-premises.md)