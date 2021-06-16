---
title: 监管本地服务帐户 | Azure Active Directory
description: 使用本指南为服务帐户创建和运行帐户生命周期过程。
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
ms.openlocfilehash: e02755f748cffcf7c523ce3b9c6fc651d2b1b02f
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111438911"
---
# <a name="govern-on-premises-service-accounts"></a>管理本地服务帐户

Active Directory 提供了四种类型的本地服务帐户：

* [组托管服务帐户 (gMSA)](service-accounts-group-managed.md)  
* [独立托管服务帐户 (sMSA)](service-accounts-standalone-managed.md)  
* [计算机帐户](service-accounts-computer.md)  
* [充当服务帐户的用户帐户](service-accounts-user-on-premises.md)


严格监管服务帐户至关重要，其目的是： 

* 根据服务帐户的用例要求和用途为其提供保护。  
* 管理服务帐户及其凭据的生命周期。  
* 根据服务帐户面临的风险及其附带的权限对其进行评估。  
* 确保 Active Directory 和 Azure Active Directory 中不存在已过时的、其权限可能产生广泛影响的服务帐户。

## <a name="principles-for-creating-a-new-service-account"></a>有关创建新服务帐户的原则

创建服务帐户时，需了解下表中列出的注意事项：

| 原则| 注意事项 | 
| - |- | 
| 服务帐户映射| 将服务帐户关联到单个服务、应用程序或脚本。 |
| 所有权| 确保该帐户有一个请求并承担责任的所有者。 |
| 范围| 明确定义服务帐户的范围并预测其使用持续时间。 |
| 目的| 出于单个特定目的创建服务帐户。 |
| 权限 | 遵循“最小权限”原则。 为此，请执行以下操作：<li>切勿向内置组（如管理员）分配权限。<li>删除本地计算机权限（如果适用）。<li>定制访问权限，并使用 Active Directory 委托进行目录访问。<li>使用粒度精细的访问权限。<li>针对基于用户的服务帐户设置帐户过期时间和基于位置的限制。 |
| 监视和审核使用情况| 监视登录数据，确保其符合预期用途。 针对异常使用情况设置警报。 |
| | |

### <a name="set-restrictions-for-user-accounts"></a>设置用户帐户的限制

对于用作服务帐户的用户帐户，请应用以下设置：

* [帐户过期](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps&preserve-view=true)：将服务帐户设置为在其评审期过后经过设置的一段时间后自动过期，除非用户确定该帐户应该继续有效。

*  LogonWorkstations：在服务帐户可登录的位置限制其权限。 如果服务帐户在计算机本地运行并且只能访问该计算机上的资源，则限制它在其他任何位置登录。

* [不能更改密码](/powershell/module/activedirectory/set-aduser)：通过将参数设置为 false，防止服务帐户更改其自身的密码。
 
## <a name="build-a-lifecycle-management-process"></a>构建生命周期管理过程

为了保持服务帐户的安全性，必须从确定需求时开始管理这些帐户，直到解除服务帐户授权为止。 

使用以下过程进行服务帐户的生命周期管理：

1. 收集帐户的使用情况信息。
1. 将服务帐户和应用移到配置管理数据库 (CMDB)。
1. 执行风险评估或正式评审。
1. 创建服务帐户并应用限制。
1. 计划并执行定期评审。 根据需要调整权限和范围。
1. 在适当情况下取消预配帐户。

### <a name="collect-usage-information-for-the-service-account"></a>收集服务帐户的使用情况信息

收集每个服务帐户的相关业务信息。 下表列出了要收集的最小信息量，但你应该收集每个帐户存在业务案例所需的所有信息。

| 数据| 说明 |
| - | - |
| 所有者| 对服务帐户负责的用户或组 |
| 目的| 服务帐户的用途 |
| 权限（范围）| 预期权限集 |
| CMDB 链接| 服务帐户与目标脚本/应用程序和所有者之间的交叉链接 |
| 风险| 基于安全风险评估给出的风险和业务影响评分 |
| 生存期| 为帐户过期或重新认证启用计划的预期最长生存期 |
| | |

理想情况下，你要发出帐户自助服务的请求，并要求提供相关信息。 所有者可以是应用程序或企业主、IT 成员或基础结构所有者。 如果帐户已获批准，使用 Microsoft Forms 等工具发出此请求并要求提供相关信息，可以更轻松地将其移植到 CMDB 库存工具。

### <a name="onboard-service-account-to-cmdb"></a>将服务帐户加入 CMDB

将收集的信息存储在 CMDB 类型的应用程序中。 除业务信息以外，还要包含与其他基础结构、应用和过程之间的所有依赖关系。  此中心存储库可以简化以下工作：

* 评估风险。  
* 为服务帐户配置所需的限制。  
* 了解任何相关的功能和安全依赖关系。  
* 展开定期评审，确定安全需求和后续需求。  
* 联系所有者来评审、停用和更改服务帐户。

考虑创建一个用于运行网站并有权连接到一个或多个人力资源 (HR) SQL 数据库的服务帐户。 下表列出了在 CMDB 中存储的服务帐户的信息，包括示例说明：

|数据 | 示例说明|
| - | - |
| 所有者、代理人| John Bloom、Anna Mayers |
| 目的| 运行 HR 网页并连接到 HR 数据库。 可以模拟正在访问数据库的最终用户。 |
| 权限、范围| HR-WEBServer：本地登录；运行网页<br>HR-SQL1：本地登录；所有 HR 数据库的读取权限<br>HR-SQL2：本地登录；只有薪金数据库的读取权限 |
| Cost Center| 883944 |
| 评估的风险| 中等；业务影响性：中等；私密信息；中等 |
| 帐户限制| 登录到：仅限前面所述的服务器；不能更改密码；MBI-Password 策略； |
| 生存期| 非受限 |
| 评审周期| 一年两次（由所有者、安全团队、隐私部门执行） |
| | |

### <a name="perform-a-risk-assessment-or-formal-review-of-service-account-usage"></a>对服务帐户的使用执行风险评估或正式评审

假设你的帐户受到未经授权的来源威胁。 评估帐户可能会给与之关联的应用程序、服务和基础结构带来的风险。 同时考虑直接和间接风险。 

* 未经授权的用户可以直接访问哪些内容？  
* 服务帐户可以访问其他哪些信息或系统？  
* 是否可以使用该帐户授予其他权限？  
* 权限更改时如何知道这一情况？

执行并记录风险评估后，你可能会发现这些风险会影响：

* 帐户限制。  
* 帐户生存期。  
* 帐户评审要求（频率和审阅者）。

### <a name="create-a-service-account-and-apply-account-restrictions"></a>创建服务帐户并应用帐户限制

仅在完成风险评估并在 CMDB 中记录了相关信息后，才创建服务帐户。 使帐户限制与风险评估保持一致。 考虑与评估相关的以下限制：

* 对于用作服务帐户的所有用户帐户，定义实际且明确的结束日期。 使用“帐户过期”标志设置此日期。 有关详细信息，请参阅 [Set-ADAccountExpiration](/powershell/module/activedirectory/set-adaccountexpiration)。 

* 登录到 [LogonWorkstation](/powershell/module/activedirectory/set-aduser)。

* [密码策略](../../active-directory-domain-services/password-policy.md)要求。

* 在[组织单位位置](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous)创建帐户，可确保仅对允许的用户进行管理。

* 设置审核并收集审核信息，以[检测](/windows/security/threat-protection/auditing/audit-directory-service-changes)对服务帐户和[服务帐户使用情况](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html)的更改。

准备将服务帐户投入生产时，请以更安全地方式授予对该帐户的访问权限。 

### <a name="schedule-regular-reviews-of-service-accounts"></a>计划服务帐户的定期评审

为分类为中等风险和高风险的服务帐户设置定期评审。 评审中应包括： 

* 所有者证明为何需要继续使用该帐户，并给出需要权限和权限范围的理由。

* 由隐私和安全团队进行评审，包括上游和下游连接的评估。

* 审核的数据，确保数据仅用于预期用途。

### <a name="deprovision-service-accounts"></a>取消预配服务帐户

在取消预配过程中，先删除权限和监视，然后在适当情况下删除帐户。

出现以下情况时需取消预配服务帐户：

* 为其创建了服务帐户的脚本或应用程序已停用。

* 使用该服务帐户的脚本或应用程序中的功能（例如，访问特定的资源）已停用。

* 该服务帐户已替换为另一服务帐户。

删除所有权限后，请执行以下操作来删除帐户：

1. 取消预配关联的应用程序或脚本后，请监视关联的服务帐户的登录和资源访问，确保没有在其他过程中使用这些信息。 如果你确定不再需要该服务帐户，请转到下一步。

1. 禁用服务帐户以防止登录该帐户，并确保不再需要该帐户。 创建一个业务策略，规定帐户应在哪个时间保持禁用状态。

1. 履行“保持禁用”策略后，请删除服务帐户。 

   * 对于 MSA：使用 PowerShell [卸载服务帐户](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps&preserve-view=true)，或者从托管服务帐户容器中手动将其删除。

   * 对于计算机或用户帐户：从 Active Directory 中手动删除帐户。

## <a name="next-steps"></a>后续步骤

若要详细了解如何保护服务帐户，请参阅以下文章：

* [本地服务帐户简介](service-accounts-on-premises.md)  
* [保护组托管服务帐户](service-accounts-group-managed.md)  
* [保护独立的托管服务帐户](service-accounts-standalone-managed.md)  
* [保护计算机帐户](service-accounts-computer.md)  
* [保护用户帐户](service-accounts-user-on-premises.md)
