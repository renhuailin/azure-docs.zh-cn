---
title: Azure AD Connect 云同步新建代理配置
description: 本文介绍如何安装云同步。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eacf3cad0a610ae75f38dbb6f1bd7bdab816d54
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613177"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>为 Azure AD Connect 云同步创建新配置

安装 Azure AD Connect 预配代理后，需要登录到 Azure 门户并对其进行配置。 按照以下步骤启用代理。

## <a name="configure-provisioning"></a>配置设置
若要配置预配，请执行以下步骤。

 1. 在 Azure 门户中，选择 " **Azure Active Directory**
 2. 选择“Azure AD Connect”。
 3. 选择 " **管理云同步**"。

 ![管理预配](media/how-to-install/install-6.png)
 
 4. 选择 " **新配置**"。
 5. 在 "配置" 屏幕上，选择你的域以及是否启用密码哈希同步。 单击 " **创建**"。  
 
 ![创建新配置](media/how-to-configure/configure-1.png)


 6.  将打开 "编辑预配配置" 屏幕。

   ![编辑配置](media/how-to-configure/con-1.png)

 7. 输入 **通知电子邮件**。 设置不正常时，此电子邮件将收到通知。  建议你始终 **禁止启用意外删除** ，并将 **意外删除阈值** 设置为你希望收到通知的数字。  有关详细信息，请参阅下面的 [意外删除](#accidental-deletions) 。
 8. 将选择器移动到 "启用"，然后选择 "保存"。

## <a name="scope-provisioning-to-specific-users-and-groups"></a>向特定用户和组设置作用域
可以通过使用本地 Active Directory 组或组织单位来确定代理的范围，以便同步特定的用户和组。 不能在配置中配置组和组织单位。 

 1.  在 Azure 门户中，选择“Azure Active Directory”。 
 2. 选择“Azure AD Connect”。
 3. 选择 " **管理云同步**"。
 4. 在 " **配置**" 下，选择您的配置。

 ![“配置”部分](media/how-to-configure/scope-1.png)
 
 5. 在 " **配置**" 下，选择 " **编辑范围筛选器** " 以更改配置规则的作用域。
 ![编辑作用域](media/how-to-configure/scope-3.png)
 7. 在右侧，可以更改范围。  单击 " **完成**  "，完成后 **保存** 。
 8. 更改作用域后，应 [重新启动预配](#restart-provisioning) ，以便立即同步更改。

## <a name="attribute-mapping"></a>属性映射
Azure AD Connect 云同步可让你轻松地在本地用户/组对象与 Azure AD 中的对象之间映射属性。  可以根据业务需求自定义默认的属性映射。 因此，可以更改或删除现有属性映射，或创建新的属性映射。  有关详细信息，请参阅 [属性映射](how-to-attribute-mapping.md)。

## <a name="on-demand-provisioning"></a>按需预配
Azure AD Connect 云同步，可以通过将这些更改应用于单个用户或组来测试配置更改。  你可以使用它来验证并验证对配置所做的更改是否已正确应用并正确地同步到 Azure AD。  有关详细信息，请参阅按 [需预配](how-to-on-demand-provision.md)。

## <a name="accidental-deletions"></a>意外删除
意外删除功能旨在防止发生意外的配置更改，以及对可能会影响许多用户和组的本地目录的更改。  可以使用此功能实现以下操作：

- 配置阻止自动删除的功能。 
- 将 (阈值) 的对象数设置为超过配置将生效的值 
- 设置通知电子邮件地址，以便在此方案中将相关同步作业放入隔离后，他们可以收到电子邮件通知 

有关详细信息，请参阅 [意外删除](how-to-accidental-deletes.md)

## <a name="quarantines"></a>隔离
云同步监视配置的运行状况，并将不正常的对象置于隔离状态。 如果对目标系统进行的大部分或全部调用由于错误（例如，无效的管理员凭据）而失败，则同步作业将被标记为隔离。  有关详细信息，请参阅关于 [隔离](how-to-troubleshoot.md#provisioning-quarantined-problems)的故障排除部分。

## <a name="restart-provisioning"></a>重新启动预配 
如果不想等待下一次计划的运行，请使用 " **重新启动设置** " 按钮触发设置运行。 
 1.  在 Azure 门户中，选择“Azure Active Directory”。 
 2. 选择“Azure AD Connect”。
 3.  选择 " **管理云同步**"。
 4. 在 " **配置**" 下，选择您的配置。

   ![用于重新启动预配的配置选择](media/how-to-configure/scope-1.png)

 5. 在顶部，选择 " **重新启动设置**"。

## <a name="remove-a-configuration"></a>删除配置
若要删除配置，请执行以下步骤。

 1.  在 Azure 门户中，选择“Azure Active Directory”。 
 2. 选择“Azure AD Connect”。
 3. 选择 " **管理云同步**"。
 4. 在 " **配置**" 下，选择您的配置。
   
   ![要删除配置的配置选择](media/how-to-configure/scope-1.png)

 5. 在配置屏幕的顶部，选择 " **删除**"。

>[!IMPORTANT]
>在删除配置之前，没有确认。 在选择 " **删除**" 之前，请确保这是要执行的操作。


## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)
