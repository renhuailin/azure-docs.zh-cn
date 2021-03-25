---
title: 监管本地服务帐户 | Azure Active Directory
description: 有关为服务帐户创建并运行帐户生命周期过程的指南
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
ms.openlocfilehash: 36ad7cf7fe2ca1ddcb592e895014b1d956e55e1b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557363"
---
# <a name="governing-on-premises-service-accounts"></a>监管本地服务帐户

Windows Active Directory 中有四种类型的本地服务帐户：

* [组托管服务帐户](service-accounts-group-managed.md) (gMSA)

* [独立托管服务帐户](service-accounts-standalone-managed.md) (sMSA)

* [计算机帐户](service-accounts-computer.md)

* [充当服务帐户的用户帐户](service-accounts-user-on-premises.md)


监管服务帐户至关重要，其目的是： 

* 根据服务帐户的用例要求和用途为其提供保护。

* 管理服务帐户及其凭据的生命周期。

* 根据服务帐户面临的风险及其附带的权限对其进行评估。 

* 确保 Active Directory 和 Azure Active Directory 中不存在已过时的、其权限可能产生广泛影响的服务帐户。

## <a name="principles-for-creating-a-new-service-account"></a>有关创建新服务帐户的原则

创建新的服务帐户时，请遵守以下准则。

| 原理| 注意事项 | 
| - |- | 
| 服务帐户映射| 将服务帐户关联到单个服务、应用程序或脚本。 |
| 所有权| 确保该帐户有一个请求并承担责任的所有者。 |
| 范围| 明确定义服务帐户的范围并预测其使用持续时间。 |
| 目的| 出于单个特定目的创建服务帐户。 |
| Privilege| 按如下所述应用最低特权原则： <br>切勿将服务帐户分配给“管理员”等内置组。<br> 在适当情况下删除本地计算机特权。<br>定制访问权限并使用 Active Directory 委托进行目录访问。<br>使用粒度精细的访问权限。<br>针对基于用户的服务帐户设置帐户过期时间和基于位置的限制 |
| 监视和审核使用情况| 监视登录数据，确保其与预期用途相匹配。 针对异常使用情况设置警报。 |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>针对用户帐户强制实施最低特权原则，并限制帐户滥用

对用作服务帐户的用户帐户使用以下设置：

* [**帐户过期**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)：将服务帐户设置为在其评审期过后经过设置的一段时间后自动过期，除非确定它应该继续有效

*  **LogonWorkstations**：在服务帐户可登录的位置方面限制其权限。 如果服务帐户在计算机本地运行并只能访问该计算机上的资源，则限制它在其他任何位置登录。

* [**不能更改密码**](/powershell/module/addsadministration/set-aduser)：通过将参数设置为 false，防止服务帐户更改其自身的密码。

 
## <a name="build-a-lifecycle-management-process"></a>构建生命周期管理过程

为了保持服务帐户的安全性，必须从确定需求的那一时间起就开始对其进行管理，直到服务帐户授权已解除为止。 

使用以下过程进行服务帐户的生命周期管理：

1. 收集帐户的使用情况信息
1. 将服务帐户和应用加入配置管理数据库 (CMDB)
1. 执行风险评估或正式评审
1. 创建服务帐户并应用限制。
1. 计划并执行定期评审。 根据需要调整权限和范围。
1. 在适当情况下取消预配帐户。

### <a name="collect-usage-information-for-the-service-account"></a>收集服务帐户的使用情况信息

收集每个服务帐户的相关业务信息。 下表显示了至少要收集的信息，但你应该收集为了说明帐户是否应该存在而制定业务案例所需的全部信息。

| 数据| 详细信息 |
| - | - |
| “所有者”| 对服务帐户负责的用户或组 |
| 目的| 服务帐户的用途 |
| 权限（范围）| 预期权限集 |
| 配置管理数据库 (CMDB) 链接| 服务帐户与目标脚本/应用程序和所有者之间的交叉链接 |
| 风险| 基于安全风险评估给出的风险和业务影响性评分 |
| 生存期| 为帐户过期或重新认证启用计划的预期最长期限 |


 

理想情况下，请发出帐户自助服务的请求，并要求提供相关信息。 所有者，可以是应用程序所有者或企业主、IT 成员或基础结构所有者。 如果帐户已获批准，使用 Microsoft 窗体等工具发出此请求并要求提供相关信息可以轻松将信息移植到 CMDB 库存工具。

### <a name="onboard-service-account-to-cmdb"></a>将服务帐户加入 CMDB

将收集的信息存储在 CMDB 类型的应用程序中。 除业务信息以外，还要包含与其他基础结构、应用和过程之间的所有依赖关系。  此中心存储库可以简化以下工作：

* 评估风险。

* 为服务帐户配置所需的限制。

* 了解相关的功能和安全依赖关系。

* 展开定期评审，确定安全需求和后续需求。

* 联系所有者来评审、停用和更改服务帐户。

考虑创建一个用于运行网站并有权连接到一个或多个 SQL 数据库的服务帐户。 在 CMDB 中为此服务帐户存储的信息可能是：

|数据 | 详细信息|
| - | - |
| 所有者、代理人| John Bloom、Anna Mayers |
| 目的| 运行 HR 网页并连接到 HR 数据库。 可以模拟正在访问数据库的最终用户。 |
| 权限、范围| HR-WEBServer：本地登录，运行网页<br>HR-SQL1：本地登录，读取所有 HR* 数据库<br>HR-SQL2：本地登录，读取所有 SALARY* 数据库 |
| Cost Center| 883944 |
| 评估的风险| 中等；业务影响性：中等；私密信息；中等 |
| 帐户限制| 登录到：仅限前面所述的服务器；不能更改密码；MBI-Password 策略； |
| 生存期| 不受限制 |
| 评审周期| 一年两次（由所有者、安全团队、隐私部门执行） |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>对服务帐户的使用执行风险评估或正式评审

根据帐户的权限和用途，评估该帐户在遭到入侵时对其关联的应用程序或服务以及基础结构可能带来的风险。 同时考虑直接和间接风险。 

* 对手可以直接访问哪些内容？

* 服务帐户可以访问其他哪些信息或系统？

* 是否可以使用该帐户授予其他权限？

* 权限更改时如何知道这一情况？

风险评估在执行完成并进行了相关记录之后，可能会对以下方面产生影响：

* 帐户限制

* 帐户生存期

* 帐户评审要求（频率和评审者）

### <a name="create-a-service-account-and-apply-account-restrictions"></a>创建服务帐户并应用帐户限制

仅在已将相关信息记录到 CMDB 并已执行风险评估之后，才创建服务帐户。 帐户限制应与风险评估相一致。 考虑与评估相关的以下限制：

* [帐户过期](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)

   * 对于用作服务帐户的所有用户帐户，定义实际且明确的使用终止日期。 使用“帐户过期”标志指定此设置。 有关更多详细信息，请参阅 [Set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration)。 

* 登录到 ([LogonWorkstation](/powershell/module/addsadministration/set-aduser))

* [密码策略](../../active-directory-domain-services/password-policy.md)要求

* 在 [OU 位置](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous)创建，确保仅对特权用户进行管理

* 设置审核并收集审核信息，以[检测](/windows/security/threat-protection/auditing/audit-directory-service-changes)对服务帐户和[服务帐户使用情况](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html)的更改。

准备好投放生产时，以安全方式向服务帐户授予访问权限。 

### <a name="schedule-regular-reviews-of-service-accounts"></a>计划服务帐户的定期评审

对分类为中等风险和高风险的服务帐户设置定期评审。 评审中应包括： 

* 所有者证明为何需要继续使用该帐户，并给出需要特权和权限范围的理由。

* 由隐私和安全团队进行评审，包括上游和下游连接的评估。

* 审核的数据，确保数据仅用于预期用途

### <a name="deprovision-service-accounts"></a>取消预配服务帐户

在取消预配过程中，先删除权限和监视，然后在适当情况下删除帐户。

出现以下情况时需取消预配服务帐户：

* 为其创建了服务帐户的脚本或应用程序已停用。

* 使用该服务帐户的脚本或应用程序中的功能（例如，访问特定的资源）已停用。

* 该服务帐户已替换为另一服务帐户。

删除所有权限后，请使用此过程删除帐户。

1. 取消预配关联的应用程序或脚本后，监视关联的服务帐户的登录和资源访问，以确保没有在其他过程中使用它。 如果你确定不再需要该服务帐户，请转到下一步。

2. 禁止该服务帐户登录，并确保不再需要该帐户。 创建一个业务策略，规定在哪个时间应保持禁用帐户。

3. 履行“保持禁用”策略后删除服务帐户。 

   * 对于 MSA，可以使用 PowerShell [卸载服务帐户](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps)，或者从托管服务帐户容器中手动将其删除。

   * 对于计算机或用户帐户，可以从 Active Directory 中手动删除帐户。

## <a name="next-steps"></a>后续步骤
参阅以下文章，了解如何保护服务帐户

* [本地服务帐户简介](service-accounts-on-premises.md)

* [保护组托管服务帐户](service-accounts-group-managed.md)

* [保护独立托管服务帐户](service-accounts-standalone-managed.md)

* [保护计算机帐户](service-accounts-computer.md)

* [保护用户帐户](service-accounts-user-on-premises.md)

* [管理本地服务帐户](service-accounts-govern-on-premises.md)