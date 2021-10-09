---
title: Azure AD Connect 云同步中的按需预配
description: 本文介绍如何使用 Azure AD Connect 的云同步功能来测试配置更改。
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
ms.openlocfilehash: 20719182a9b97bc783ef2bdc16ccad327dba79aa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742581"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect 云同步中的按需预配

可以使用 Azure Active Directory (Azure AD) Connect 的云同步功能来测试配置更改，方法是将这些更改应用于单个用户。 此按需预配可帮助你验证和确认对配置所做的更改是否已适当应用并正确同步到 Azure AD。  

> [!IMPORTANT] 
> 使用按需预配时，范围筛选器不会应用于所选用户。 你可以在指定的组织单位之外的用户上使用按需预配。

有关其他信息和示例，请参阅以下视频。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mW]

## <a name="validate-a-user"></a>验证用户
若要使用按需预配，请执行以下步骤：

1.  在 Azure 门户中，选择“Azure Active Directory”。 
2.  选择“Azure AD Connect”。
3.  选择“管理云同步”。

    ![屏幕截图，显示用于管理云同步的链接。](media/how-to-install/install-6.png)
4. 在“配置”下选择你的配置。
5. 在“验证”下选择“预配用户”按钮。  

   ![屏幕截图，显示用于预配用户的按钮。](media/how-to-on-demand-provision/on-demand-2.png)

6. 在“按需预配”屏幕上输入用户的可分辨名称，然后选择“预配”按钮。   
 
   ![屏幕截图，显示用户名和“预配”按钮。](media/how-to-on-demand-provision/on-demand-3.png)
7. 预配完成后，会显示包含四个绿色对勾标记的成功屏幕。 任何错误都显示在左侧。

   ![屏幕截图，显示成功进行预配。](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>获取有关预配的详细信息
现在，你可以查看用户信息并确定是否已应用在配置中所做的更改。 本文其余部分介绍了会在成功同步的用户的详细信息中显示的各个部分。

### <a name="import-user"></a>导入用户
“导入用户”部分提供已从 Active Directory 导入的用户的信息。 这是用户在预配到 Azure AD 中之前的外观。 选择“查看详细信息”链接以显示此信息。

![屏幕截图，显示一个用于查看已导入用户的详细信息的按钮。](media/how-to-on-demand-provision/on-demand-5.png)

你可以通过此信息查看各种已导入的属性（及其值）。 如果创建了自定义属性映射，则可在此处看到值。

![屏幕截图显示了用户详细信息。](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>确定用户是否在范围内
“确定用户是否在范围内”部分说明了导入到 Azure AD 的用户是否在范围内。 选择“查看详细信息”链接以显示此信息。

![屏幕截图，显示一个用于查看用户范围详细信息的按钮。](media/how-to-on-demand-provision/on-demand-7.png)

可以通过此信息查看用户是否在范围内。

![屏幕截图显示了用户范围详细信息。](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>在源系统和目标系统之间匹配用户
“在源系统和目标系统之间匹配用户”部分说明了用户是否已存在于 Azure AD 中，以及是否应进行联接（而不是预配新用户）。 选择“查看详细信息”链接以显示此信息。

![屏幕截图，显示一个用于查看已匹配用户的详细信息的按钮。](media/how-to-on-demand-provision/on-demand-8.png)

可以通过此信息查看是否找到了匹配项，或者是否要创建新用户。

![屏幕截图显示了用户信息。](media/how-to-on-demand-provision/on-demand-11.png)

匹配的详细信息显示了一条消息，其中包含以下三个操作之一：
- **创建**：在 Azure AD 中创建用户。
- **更新**：根据配置中所做的更改更新用户。
- **删除**：从 Azure AD 中删除用户。

消息因执行的操作的类型而异。

### <a name="perform-action"></a>执行操作
“执行操作”部分说明了已在应用配置后预配或导出到 Azure AD 中的用户。 这是用户在预配到 Azure AD 中之后的外观。 选择“查看详细信息”链接以显示此信息。

![屏幕截图，显示一个用于查看已执行操作的详细信息的按钮。](media/how-to-on-demand-provision/on-demand-9.png)

可以通过此信息在应用配置后查看属性的值。 它们看上去是类似于导入的内容还是有所不同？ 配置是否已成功应用？  

此过程使你能够在属性转换通过云进行移动并进入 Azure AD 租户中时对其进行跟踪。

![屏幕截图，显示所跟踪属性的详细信息。](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>后续步骤 

- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)
- [安装 Azure AD Connect 云同步](how-to-install.md)
 