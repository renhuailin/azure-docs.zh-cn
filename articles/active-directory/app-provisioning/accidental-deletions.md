---
title: 在 Azure Active Directory 中的应用程序预配中启用意外删除防护
description: 在 Azure Active Directory 中的应用程序预配中启用意外删除防护。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 09/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 6f7cb755663b7bf454e33c5a4f785d29d862967d
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155413"
---
# <a name="enable-accidental-deletions-prevention-in-the-azure-ad-provisioning-service-preview"></a>在 Azure AD 预配服务中启用意外删除防护（预览版）

Azure AD 预配服务包含一项有助于避免意外删除的功能。 此功能确保不会在应用程序中意外禁用或删除用户。 

该功能允许指定删除阈值，一旦超过该阈值，管理员就需要显式选择允许处理删除操作。

> [!NOTE]
> Workday/SuccessFactors 集成不支持意外删除。 它也不支持对范围进行更改（例如，更改范围筛选器，或者从“同步所有用户和组”更改为“同步分配的用户和组”）。 在意外删除防护功能完全发布之前，需要使用以下 URL 访问 Azure 门户： https://aka.ms/AccidentalDeletionsPreview


## <a name="configure-accidental-deletion-prevention"></a>配置意外删除防护
若要启用意外删除防护，请执行以下操作：
1.  在 Azure 门户中，选择“Azure Active Directory”。 
2.  选择“企业应用程序”，然后选择你的应用。
3.  选择“预配”，然后在“预配”页上选择“编辑预配” 。
4. 在“设置”下，选中“防止意外删除”复选框并指定删除阈值 。 此外，还请务必填写通知电子邮件地址。 如果达到删除阈值，系统将发送电子邮件。
5. 选择“保存”以保存更改。

达到删除阈值时，作业将进入隔离区，并且系统会发送通知电子邮件。 然后可以允许或拒绝已隔离的作业。 若要详细了解隔离行为，请参阅[隔离状态下的应用程序预配](application-provisioning-quarantine-status.md)。

## <a name="known-limitations"></a>已知的限制
需要知道两项重要限制，我们正在努力解决这些限制：
- Workday 和 SuccessFactors 的 HR 驱动型预配不支持意外删除功能。 
- 意外删除功能不支持对预配配置进行更改（例如更改范围）。 

## <a name="recovering-from-an-accidental-deletion"></a>在意外删除后进行恢复
如果你遇到意外删除问题，预配状态页上会指出该问题。  该页上会显示“已隔离预配。有关详细信息，请查看隔离详细信息”。

可以单击“允许删除”或“查看预配日志” 。

### <a name="allowing-deletions"></a>允许删除

“允许删除”操作将删除触发意外删除阈值的对象。  请按照以下过程接受删除。  

1. 选择“允许删除”。
2. 对于确认单击“是”以允许删除。
3. 你将看到关于已接受删除的确认，并且状态将返回到下一周期的正常状态。

### <a name="rejecting-deletions"></a>拒绝删除

如果不想允许删除，则需要执行以下操作：
- 调查发生删除的原因。 可以使用预配日志来了解详细信息。
- 通过再次将用户/组分配到应用、还原用户/组或更新预配配置，来防止删除。
- 进行必要的更改以防止删除用户/组后，重新开始预配。 在进行必要的更改以防止删除用户/组之前，请不要重新开始预配。 


### <a name="test-deletion-prevention"></a>测试删除防护
可通过以下方式测试该功能：将阈值设置为一个较小的数字（例如 3）以触发禁用/删除事件，然后更改范围筛选器，取消分配用户，并从目录中删除用户（请参阅下一部分中的常见场景）。 

让预配作业运行 20 – 40 分钟，然后导航回到预配页。 你将看到预配作业已进入隔离区，并可以选择允许删除或查看预配日志，以了解发生删除的原因。

## <a name="common-de-provisioning-scenarios-to-test"></a>要测试的常见取消预配场景
- 删除用户/将用户放入回收站。
- 阻止用户登录。
- 从应用程序中将用户或组取消分配。
- 从为用户提供应用访问权限的组中删除用户。

若要详细了解取消预配方案，请参阅[应用程序预配的工作原理](how-provisioning-works.md#de-provisioning)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-scenarios-count-toward-the-deletion-threshold"></a>在计算是否达到删除阈值时，哪些场景会计入？
在计算是否达到删除阈值时，如果用户设置为从目标应用程序中删除，那么这就会算作一次删除。 可能导致从目标应用程序中删除用户的场景可包括：从应用程序中取消分配用户，以及软删除/硬删除目录中的用户。 在计算是否达到删除阈值时，将计入已评估为要删除的组。 此功能除了适用于删除之外，也适用于禁用。

### <a name="what-is-the-interval-that-the-deletion-threshold-is-evaluated-on"></a>删除阈值的评估间隔是多少？
在每个周期都会评估删除阈值。 如果在单个周期内删除次数不超过阈值，则不会触发“断路器”。 如果需要完成多个周期才能进入稳定状态，则会在每个周期评估删除阈值。

### <a name="how-are-these-deletion-events-logged"></a>这些删除事件是如何记录的？
可以查找应该禁用/删除但由于删除阈值的原因而未禁用/删除的用户。 导航到“预配日志”，然后使用“StagedAction”或“StagedDelete”筛选“操作”  。


## <a name="next-steps"></a>后续步骤 

- [应用程序预配工作原理](how-provisioning-works.md)
- [规划应用程序预配部署](plan-auto-user-provisioning.md)
