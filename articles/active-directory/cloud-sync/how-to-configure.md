---
title: Azure AD Connect 云同步新代理配置
description: 本文介绍如何安装云同步。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1a8602b18c4ec68eacc37faca941211ee83fd63
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742641"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>为 Azure AD Connect 云同步创建新配置

以下文档将指导你配置 Azure AD Connect 云同步。有关如何配置云同步的其他信息和示例，请参阅下面的视频。


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWKact]


## <a name="configure-provisioning"></a>配置预配
若要配置预配，请执行以下步骤。

 1. 在 Azure 门户中，选择“Azure Active Directory”
 2. 选择“Azure AD Connect”。
 3. 选择“管理云同步”。

 ![管理预配](media/how-to-install/install-6.png)
 
 4. 选择“新配置”。
 5. 在“配置”屏幕上，选择你的域以及是否启用密码哈希同步。单击“创建”。  
 
 ![创建新配置](media/how-to-configure/configure-1.png)


 6.  将打开“编辑预配配置”屏幕。

   ![编辑配置](media/how-to-configure/con-1.png)

 7. 输入“通知电子邮件”。 预配不正常时，此电子邮件将收到通知。  建议始终启用“防止意外删除”，并将“意外删除阈值”设置为你希望收到通知的次数。  有关详细信息，请参阅下面的[意外删除](#accidental-deletions)。
 8. 将选择器移动到“启用”，然后选择“保存”。

## <a name="scope-provisioning-to-specific-users-and-groups"></a>向特定用户和组预配范围
可以通过使用本地 Active Directory 组或组织单位来确定代理的范围，以便同步特定的用户和组。 不能在配置中配置组和组织单位。 

 1.  在 Azure 门户中，选择“Azure Active Directory”。 
 2. 选择“Azure AD Connect”。
 3. 选择“管理云同步”。
 4. 在“配置”下选择你的配置。

 ![“配置”部分](media/how-to-configure/scope-1.png)
 
 5. 在“配置”下选择“编辑范围筛选器”，以更改配置规则的范围。
 ![编辑范围](media/how-to-configure/scope-3.png)
 7. 在右侧，可以更改范围。  单击“完成”，并在完成后单击“保存”。
 8. 更改范围后，应[重启预配](#restart-provisioning)，以便立即同步更改。

## <a name="attribute-mapping"></a>属性映射
通过 Azure AD Connect 云同步，可轻松地在本地用户/组对象与 Azure AD 中的对象之间映射属性。  可以根据业务需求自定义默认的属性映射。 因此，可以更改或删除现有属性映射，或创建新的属性映射。  有关详细信息，请参阅[属性映射](how-to-attribute-mapping.md)。

## <a name="on-demand-provisioning"></a>按需预配
Azure AD Connect 云同步允许你通过将这些更改应用于单个用户或组来测试配置更改。  可以使用此功能验证和确认对配置所做的更改是否已适当应用并正确同步到 Azure AD。  有关详细信息，请参阅[按需预配](how-to-on-demand-provision.md)。

## <a name="accidental-deletions"></a>意外删除
意外删除功能旨在防止发生意外的配置更改，以及防止发生影响许多用户和组的本地目录更改。  可以使用此功能实现以下操作：

- 配置阻止自动意外删除的功能。 
- 设置对象数上限（阈值），超过该值配置将会生效 
- 设置通知电子邮件地址，以便此方案中相关同步作业被置于隔离状态后，用户可以收到电子邮件通知 

有关详细信息，请参阅[意外删除](how-to-accidental-deletes.md)

## <a name="quarantines"></a>隔离
云同步监视配置的运行状况，并将不正常的对象置于隔离状态。 如果某个错误（例如管理员凭据无效）导致针对目标系统发出的大部分或所有调用持续失败，则同步作业将标记为“处于隔离状态”。  有关详细信息，请参阅关于[隔离](how-to-troubleshoot.md#provisioning-quarantined-problems)的故障排除部分。

## <a name="restart-provisioning"></a>重新启动预配 
如果不想等待下一次计划的运行，请使用“重启预配”按钮触发预配运行。 
 1.  在 Azure 门户中，选择“Azure Active Directory”。 
 2. 选择“Azure AD Connect”。
 3.  选择“管理云同步”。
 4. 在“配置”下选择你的配置。

   ![用于重启预配的配置选择](media/how-to-configure/scope-1.png)

 5. 在顶部，选择“重启预配”。

## <a name="remove-a-configuration"></a>删除配置
若要删除配置，请执行以下步骤。

 1.  在 Azure 门户中，选择“Azure Active Directory”。 
 2. 选择“Azure AD Connect”。
 3. 选择“管理云同步”。
 4. 在“配置”下选择你的配置。
   
   ![用于删除配置的配置选择](media/how-to-configure/scope-1.png)

 5. 在配置屏幕的顶部，选择“删除”。

>[!IMPORTANT]
>在删除配置之前，没有确认。 在选择“删除”之前，请确保这是要执行的操作。


## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)
