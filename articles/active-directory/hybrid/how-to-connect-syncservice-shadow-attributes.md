---
title: Azure AD Connect 同步服务影子属性 | Microsoft Docs
description: 介绍影子属性在 Azure AD Connect 同步服务中的工作方式。
services: active-directory
author: billmath
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/29/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b2274099803961fb477f0929c801e2fc341dd4b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355267"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect 同步服务影子属性
大多数属性在 Azure AD 中的表示方式与在本地 Active Directory 中相同。 但某些属性有一些特殊处理，并且 Azure AD 中的属性值可能不同于 Azure AD Connect 同步的值。

## <a name="introducing-shadow-attributes"></a>引入了影子属性
某些属性在 Azure AD 中有两种表示形式。 将存储本地值和计算值。 这些额外的属性称为影子属性。 表示此行为的两个最常用属性是 **userPrincipalName** 和 **proxyAddress**。 当这些属性中有表示非已验证域的值时，属性值将发生更改。 但 Connect 中的同步引擎可读取影子属性的值，因此从其角度来看，该属性已由 Azure AD 确认。

无法使用 Azure 门户或 PowerShell 查看影子属性。 但对于属性在本地和云中具有不同值的某些方案，了解此概念可帮助对这些方案进行故障排除。

若要更好地了解此行为，请查看 Fabrikam 中的此示例：  
![屏幕截图显示了几个示例的 Active Directory UPN后 缀，相应的“Azure AD 域”值分别为“未添加”、“未验证”和“已验证”。](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
这些域在其本地 Active Directory 中有多个 UPN 后缀，但只验证了其中一个。

### <a name="userprincipalname"></a>userPrincipalName
用户在非已验证域中具有以下属性值：

| Attribute | 值 |
| --- | --- |
| 本地 userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

userPrincipalName 属性是在使用 PowerShell 时显示的值。

由于实际本地属性值存储在 Azure AD 中，因此验证 fabrikam.com 域时，Azure AD 使用 shadowUserPrincipalName 的值更新 userPrincipalName 属性。 对于要更新的这些值，无需从 Azure AD Connect 同步任何更改。

### <a name="proxyaddresses"></a>proxyAddresses
用于仅包括已验证域的相同过程也会对 proxyAddresses 执行，但会使用某个额外的逻辑。 仅对邮箱用户进行已验证域检查。 已启用邮件的用户或联系人表示另一个 Exchange 组织中的用户，只能将 proxyAddresses 中的任何值添加到这些对象。

对于邮箱用户（无论是在本地还是在 Exchange Online 中），将仅显示已验证域的值。 它可能如下所示：

| Attribute | 值 |
| --- | --- |
| 本地 proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

在本例中，**smtp:abbie.spencer\@fabrikam.com** 已删除，因为该域尚未验证。 但是，Exchange 还添加了 **SIP:abbie.spencer\@fabrikamonline.com**。 Fabrikam 未使用本地 Lync/Skype，但 Azure AD 和 Exchange Online 准备使用它。

proxyAddresses 的此逻辑称为 **ProxyCalc**。 在以下情况下，每次更改用户时，将调用 ProxyCalc：

- 为用户分配了包含 Exchange Online 的服务计划，即使未授权该用户使用 Exchange，也是如此。 例如，如果为用户分配了 Office E3 SKU，但仅为其分配了 SharePoint Online。 即使邮箱仍在本地，也是如此。
- 属性 msExchRecipientTypeDetails 具有值。
- 更改 proxyAddresses 或 userPrincipalName。

如果 ShadowProxyAddresses 包含未验证的域，并且没有为云用户配置以下属性之一，则 ProxyCalc 将清理某个地址。 
- 已在启用了 EXO 服务类型计划的情况下为用户授权（不包括 MyAnalytics）  
- 为用户设置了 MSExchRemoteRecipientType（非 null）  
- 用户被视为共享资源

若要将云用户视为共享资源，需在 CloudMSExchRecipientDisplayType 中为其设置以下值之一 

 |对象显示类型|值（十进制）|
 |-----|-----|
 |MailboxUser|  0|
 |DistributionGroup|    1|
 |PublicFolder| 2|
 |DynamicDistributionGroup| 3|
 |组织| 4|
 |PrivateDistributionList|  5|
 |RemoteMailUser|   6|
 |ConferenceRoomMailbox|    7|
 |EquipmentMailbox| 8|
 |ArbitrationMailbox|   10|
 |MailboxPlan|  11|
 |LinkedUser|   12|
 |RoomList| 15|
 |SyncedMailboxUser|    -2147483642|
 |SyncedUDGasUDG|   -2147483391|
 |SyncedUDGasContact|   -2147483386|
 |SyncedPublicFolder|   -2147483130|
 |SyncedDynamicDistributionGroup|   -2147482874|
 |SyncedRemoteMailUser| -2147482106|
 |SyncedConferenceRoomMailbox|  -2147481850|
 |SyncedEquipmentMailbox|   -2147481594|
 |SyncedUSGasUDG|   -2147481343|
 |SyncedUSGasContact|   -2147481338|
 |ACLableSyncedMailboxUser| -1073741818|
 |ACLableSyncedRemoteMailUser|  -1073740282|
 |ACLableSyncedUSGasContact|    -1073739514|
 |SyncedUSGasUSG|   -1073739511|
 |SecurityDistributionGroup|    1043741833|
 |SyncedUSGasUSG|   1073739511|
 |ACLableSyncedUSGasContact|    1073739514|
 |RBAC 角色组|  1073741824|
 |ACLableMailboxUser|   1073741824|
 |ACLableRemoteMailUser|    1073741830|


>[!NOTE]
> 在 Azure AD 端无法看到 CloudMSExchRecipientDisplayType，只能使用 Exchange Online cmdlet [Get-Recipient](/powershell/module/exchange/get-recipient) 之类的命令来查看它。  
>
>示例：
> ```PowerShell
>   Get-Recipient admin | fl *type*
> ```
>

ProxyCalc 可能需要一些时间来处理用户更改，并且不会与 Azure AD Connect 导出过程同步。

> [!NOTE]
> 对于本主题中未介绍的高级方案，ProxyCalc 逻辑具有一些其他行为。 本主题供你了解行为并未记录所有内部逻辑。

### <a name="quarantined-attribute-values"></a>隔离的属性值
有重复的属性值时，也使用影子属性。 有关详细信息，请参阅[重复属性复原](how-to-connect-syncservice-duplicate-attribute-resiliency.md)。

## <a name="see-also"></a>另请参阅
* [Azure AD Connect 同步](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)。
