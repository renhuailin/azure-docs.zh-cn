---
title: 排查使用条件访问时的登录问题 - Azure Active Directory
description: 本文介绍如何处理条件访问策略导致意外结果的问题
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d53f67c4035d8b97bd3117c5386b371b711a5e7b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606715"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>排查使用条件访问时的登录问题

可以参考本文中的信息，使用错误消息和 Azure AD 登录日志来排查与条件访问相关的意外登录结果。

## <a name="select-all-consequences"></a>选择“所有”后果

条件访问框架提供了极大的配置灵活性。 不过，极大的灵活性也意味着应先仔细检查每个配置策略，然后才能发布，以免产生不良后果。 在这种情况下，应该特别注意影响完整集的任务，例如 **所有用户/组/云应用**。

组织应避免以下配置：

**对于所有用户、所有云应用：**

- **阻止访问** - 此配置将阻止整个组织访问。
- **需要标记为合规的设备** - 对于尚未注册其设备的用户，此策略将阻止所有访问权限（包括对 Intune 门户的访问权限）。 如果是不具有注册设备的管理员，则此策略会阻止你回到 Azure 门户更改策略。
- **需要已建立混合 Azure AD 域联接的设备** - 如果他们不具有已建立混合 Azure AD 域联接的设备，此阻止访问权限的策略还可能会阻止组织中所有用户的访问权限。
- **需要应用保护策略** - 如果没有 Intune 策略，此阻止访问权限的策略还可能会阻止组织中所有用户的访问权限。 如果你是管理员，没有设置了 Intune 应用保护策略的客户端应用程序，则此策略会阻止你返回到 Intune 和 Azure 之类的门户。

**对于所有用户、所有云应用、所有设备平台：**

- **阻止访问** - 此配置将阻止整个组织访问。

## <a name="conditional-access-sign-in-interrupt"></a>条件访问登录中断

第一种方法是查看显示的错误消息。 对于使用 Web 浏览器登录时出现的问题，错误页面本身会包含详细信息。 此信息本身可能会描述具体的问题，并可能会建议解决方法。

![登录错误 - 需要合规的设备](./media/troubleshoot-conditional-access/image1.png)

出现上述错误时，消息会指出只能从符合公司移动设备管理策略的设备或客户端应用程序访问该应用程序。 但在此场合下，应用程序和设备并不符合该策略。

## <a name="azure-ad-sign-in-events"></a>Azure AD 登录事件

获取有关登录中断的详细信息的第二种方法是查看 Azure AD 登录事件，以确定应用了哪个（或哪些）条件访问策略，以及为何应用这个（这些）策略。

在初始错误页面中单击“更多详细信息”可以找到有关问题的详细信息。 单击“更多详细信息”会显示故障排除信息，在 Azure AD 登录事件中搜索用户看到的特定失败事件时，或者在向 Microsoft 提出支持事件时，这些信息非常有用。

![有关条件访问策略中断的 Web 浏览器登录的更多详细信息。](./media/troubleshoot-conditional-access/image2.png)

若要查明已应用哪个（或哪些）条件访问策略，以及为何应用这个（这些）策略，请执行以下操作。

1. 以全局管理员、安全管理员或全局读取者的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “登录”。
1. 找到要查看的登录事件。 添加或删除筛选器和列，以筛选掉不必要的信息。
   1. 添加筛选器以缩小范围：
      1. 想要调查的特定事件时，请添加“关联 ID”。
      1. 要查看策略失败和成功事件，请添加“条件访问”。 限定筛选器的范围，以便仅显示失败事件来限制结果。
      1. 要查看与特定用户相关的信息，请添加“用户名”。
      1. 添加已限定到相关时间范围的“日期”。

   ![在登录日志中选择“条件访问”筛选器](./media/troubleshoot-conditional-access/image3.png)

1. 找到与用户登录失败对应的登录事件后，选择“条件访问”选项卡。“条件访问”选项卡将显示导致登录中断的特定策略。
   1. “故障排除和支持”选项卡中的信息可能会明确地解释登录失败的原因，例如，设备不满足合规性要求。
   1. 若要进一步调查，请单击“策略名称”向下钻取到策略的配置。 单击“策略名称”会显示所选策略的策略配置用户界面，供你进行查看和编辑。
   1. 在登录事件的“基本信息”、“位置”、“设备信息”、“身份验证详细信息”和“其他详细信息”选项卡中，也提供了用于评估条件访问策略的客户端用户和设备详细信息。      

### <a name="policy-details"></a>策略详细信息

选择登录事件中策略右侧的省略号将显示策略详细信息。 这让管理员能进一步了解策略应用成功或失败的原因。

   ![登录事件的“条件访问”选项卡](./media/troubleshoot-conditional-access/image5.png)

   ![策略详细信息（预览版）](./media/troubleshoot-conditional-access/policy-details.png)

左侧提供登录时收集的详细信息，右侧提供的详细信息介绍这些详细信息是否满足所应用的条件访问策略的要求。 仅当满足所有条件时，或者所有条件都未配置时，条件访问策略才适用。

如果事件中的信息不足以让你了解登录结果或调整策略来获取所需结果，可以提出支持事件。 导航到该登录事件的“故障排除和支持”选项卡，然后选择“创建新的支持请求”。 

![登录事件的“故障排除和支持”选项卡](./media/troubleshoot-conditional-access/image6.png)

提交事件时，请在事件提交详细信息中提供请求 ID，以及登录事件中的时间和日期。 Microsoft 支持人员将通过此信息找到你所关注的事件。

### <a name="conditional-access-error-codes"></a>条件访问错误代码

| 登录错误代码 | 错误字符串 |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="what-to-do-if-you-are-locked-out-of-the-azure-portal"></a>如果你被锁定在 Azure 门户之外，该怎么办？

如果你因为条件访问策略中的设置不正确而被锁定在 Azure 门户之外，请执行以下操作：

- 检查组织中是否有其他管理员尚未被阻止。 具有 Azure 门户访问权限的管理员可以禁用影响你登录的策略。 
- 如果组织中没有管理员可以更新策略，请提交支持请求。 Microsoft 支持人员可以审核并在确认后更新妨碍访问的条件访问策略。

## <a name="next-steps"></a>后续步骤

- [Azure Active Directory 门户中的“登录活动”报表](../reports-monitoring/concept-sign-ins.md)
- [使用 What If 工具排查条件访问问题](troubleshoot-conditional-access-what-if.md)
