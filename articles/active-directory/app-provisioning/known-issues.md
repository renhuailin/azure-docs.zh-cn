---
title: Azure Active Directory 中的应用程序预配的已知问题
description: 了解在 Azure Active Directory 中处理自动应用程序预配时的已知问题。
author: kenwith
ms.author: kenwith
manager: mtillman
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.reviewer: arvinh
ms.openlocfilehash: 1180852bdaa88f1c717ab6a44d85d84412c5b155
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449600"
---
# <a name="known-issues-for-application-provisioning-in-azure-active-directory"></a>Azure Active Directory 中的应用程序预配的已知问题
本文讨论了在处理应用程序预配时应注意的已知问题。 若要在 UserVoice 上提供有关应用预配服务的反馈，请参阅 [Azure Active Directory (Azure AD) 应用程序预配 UserVoice](https://aka.ms/appprovisioningfeaturerequest)。 我们将密切关注 UserVoice，以便改进该服务。

> [!NOTE]
> 本文提供的已知问题列表并不完整。 如果你知道未列出的问题，请在页面底部提供反馈。

## <a name="authorization"></a>授权 

#### <a name="unable-to-save"></a>无法保存

必须填写租户 URL、机密令牌和通知电子邮件才能保存。 不能仅提供其中一个。 

#### <a name="unable-to-change-provisioning-mode-back-to-manual"></a>无法将预配模式改回手动

首次配置预配后，你会注意到预配模式已从手动切换为自动。 不能将其改回手动。 但你可以通过 UI 关闭预配。 在 UI 中关闭预配与将下拉列表设置为手动完全相同。

## <a name="attribute-mappings"></a>属性映射 

#### <a name="attribute-samaccountname-or-usertype-not-available-as-a-source-attribute"></a>属性 SamAccountName 或 userType 不可用作源属性

默认情况下，特性 SamAccountName 和 userType 不可用作源属性 。 扩展架构以添加属性。 可以通过扩展架构将属性添加到可用源属性列表。 若要了解详细信息，请参阅[缺少源属性](user-provisioning-sync-attributes-for-mapping.md)。 

#### <a name="source-attribute-dropdown-missing-for-schema-extension"></a>架构扩展缺少源属性下拉列表

有时，UI 中的源属性下拉列表中会缺少架构的扩展。 进入属性映射的高级设置，并手动添加属性。 若要了解详细信息，请参阅[自定义属性映射](customize-application-attributes.md)。

#### <a name="null-attribute-cant-be-provisioned"></a>无法预配 Null 属性

Azure AD 当前无法预配 null 属性。 如果用户对象上的属性为 null，则将跳过它。 

#### <a name="maximum-characters-for-attribute-mapping-expressions"></a>属性映射表达式的最大字符数

属性映射表达式最多可以有 10000 个字符。 

#### <a name="unsupported-scoping-filters"></a>不受支持的范围筛选器

不支持将目录扩展、appRoleAssignments、userType 和 accountExpires 属性作为范围筛选器  。

#### <a name="multivalue-directory-extensions"></a>多值目录扩展

多值目录扩展不能用于属性映射或范围筛选器。 

## <a name="service-issues"></a>服务问题 

#### <a name="unsupported-scenarios"></a>不支持的方案

- 不支持预配密码。 
- 不支持预配嵌套组。 
- 由于租户规模的原因，不支持预配到 B2C 租户。
- 目前，并非所有功能在所有云中都可用。 例如，Atlassian 在政府版云中尚不可用。 我们正与应用开发人员合作，将其应用载入所有云。

#### <a name="automatic-provisioning-isnt-available-on-my-oidc-based-application"></a>基于 OIDC 的应用程序无法使用自动预配

如果创建应用注册，则企业应用中的相应服务主体将不会启用自动用户预配。 需要请求将应用添加到库中（如果打算供多个组织使用），或创建第二个非库应用进行预配。

#### <a name="the-provisioning-interval-is-fixed"></a>预配间隔是固定的

预配周期之间的[时间](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)当前不可配置。 

#### <a name="changes-not-moving-from-target-app-to-azure-ad"></a>更改不会从目标应用移动到 Azure AD

应用预配服务不知道外部应用中所做的更改。 因此，不会执行回退操作。 应用预配服务依赖于在 Azure AD 所做的更改。 

#### <a name="switching-from-sync-all-to-sync-assigned-not-working"></a>从同步全部切换到同步已分配无效

将作用域从“同步全部”更改为“同步已分配”后，一定还要进行重启，以确保更改生效。 可以从 UI 执行重启。

#### <a name="provisioning-cycle-continues-until-completion"></a>预配周期持续直到完成

当设置预配到 `enabled = off` 或选择“停止”时，当前的预配周期将继续运行直到完成。 该服务将停止执行任何将来的循环，直到再次开启预配。

#### <a name="member-of-group-not-provisioned"></a>未预配的组成员

当某个组处于范围内并且某个成员超出范围时，将预配该组。 不会预配超出范围的用户。 如果成员返回到范围中，服务将不会立即检测到更改。 重启预配将解决此问题。 定期重启服务，以确保所有用户均已正确预配。

#### <a name="manager-isnt-provisioned"></a>管理器未预配

如果用户及其管理器都处于预配范围内，则该服务将预配用户，然后更新管理器。 如果在第一天，用户处于范围内并且管理器超出范围，我们将在不进行管理器引用的情况下预配用户。 当管理器进入范围时，在重启预配并使服务重新评估所有用户之前，将不会更新管理器引用。 

## <a name="on-premises-application-provisioning"></a>本地应用程序预配
以下信息是当前 Azure AD ECMA 连接器主机和本地应用程序预配的已知限制列表。

### <a name="application-and-directories"></a>应用程序和目录
目前尚不支持以下应用程序和目录。

#### <a name="active-directory-domain-services-user-or-group-writeback-from-azure-ad-by-using-the-on-premises-provisioning-preview"></a>Active Directory 域服务（用户或组使用本地预配预览从 Azure AD 写回）
   - 如果用户是通过 Azure AD Connect 管理的，则授权来源为本地 Azure AD。 因此，无法在 Azure AD 中更改用户属性。 此预览版不会更改 Azure AD Connect 管理的用户的授权来源。
   - 尝试使用 Azure AD Connect 和本地预配将组/用户预配至 Active Directory 域服务会导致创建循环，其中 Azure AD Connect 可以覆盖云中预配服务所做的更改。 Microsoft 正致力于提供组或用户的专用写回功能。 在[此网站](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/16887037-enable-user-writeback-to-on-premise-ad-from-azure)上投票赞成 UserVoice 反馈，以跟踪预览状态。 或者，你可以使用 [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) 用户/组从 Azure AD 写回到 Active Directory。

#### <a name="connectors-other-than-sql"></a>非 SQL 连接器

   对于泛型 SQL 连接器，正式支持 Azure AD ECMA 连接器主机。 尽管可以使用其他连接器（如 Web 服务连接器或自定义 ECMA 连接器），但它尚不受支持。

#### <a name="azure-ad"></a>Azure AD

   使用本地预配可以将 Azure AD 中已存在的用户预配到第三方应用程序中。 无法将用户加入第三方应用程序的目录。 客户需要依赖于我们的本机 HR 集成、Azure AD Connect、Microsoft Identity Manager 或 Microsoft Graph 让用户进入目录。

### <a name="attributes-and-objects"></a>属性和对象 
不支持以下属性和对象：
   - 多值属性。
   - 引用属性（例如，管理员）。
   - 组。
   - 复杂定位点（例如，ObjectTypeName + UserName）。
   - 本地应用程序有时不与 Azure AD 联合，且需要本地密码。 本地预配预览不支持预配一次性密码或在 Azure AD 与第三方应用程序之间同步密码。
   - 不支持 export_password 虚拟属性、SetPassword 和 ChangePassword 操作。

#### <a name="ssl-certificates"></a>SSL 证书数
   Azure AD ECMA 连接器主机当前需要 Azure 信任的 SSL 证书或要使用的预配代理。 证书主体必须与安装 Azure AD ECMA 连接器主机的主机名匹配。

#### <a name="anchor-attributes"></a>定位点属性
   当前 Azure AD ECMA 连接器主机不支持定位点属性更改（重命名）或目标系统，这需要多个属性来形成定位点。 

#### <a name="attribute-discovery-and-mapping"></a>属性发现和映射
   目标应用程序支持的属性在属性映射的 Azure 门户中显示。 将继续发现新添加的属性。 如果属性类型已更改（例如，字符串更改为布尔值），并且该属性是映射的一部分，Azure 门户中的类型不会自动改变。 客户需要进入映射中的高级设置并手动更新属性类型。

## <a name="next-steps"></a>后续步骤
[预配工作原理](how-provisioning-works.md)
