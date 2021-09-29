---
title: Azure AD 条件访问中的自定义控件
description: 了解 Azure Active Directory 条件访问中的自定义控件的工作原理。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b42df2aeb70d9dcdc9ef77ed224d50a352097ec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592824"
---
# <a name="custom-controls-preview"></a>自定义控件（预览版）

自定义控件是 Azure Active Directory 的预览功能。 使用自定义控件时，用户将被重定向到兼容服务，以满足 Azure Active Directory 之外的身份验证要求。 若要满足此控件要求，用户浏览器将重定向到外部服务，执行任何所需的身份验证，然后重定向回 Azure Active Directory。 Azure Active Directory 将验证响应，如果用户已成功完成身份验证或验证，该用户将继续留在条件访问流中。

> [!NOTE]
> 有关我们计划对自定义控件功能进行的更改的详细信息，请参阅 2020 年 2 月版[新增功能存档](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls)。

## <a name="creating-custom-controls"></a>创建自定义控件

自定义控件使用有限的一组已批准身份验证提供程序。 若要创建自定义控件，应首先联系想使用的控件的提供商。 每个非 Microsoft 提供商在注册、订阅或以其他方式加入服务以及指示想要与条件访问集成方面都有自己的进程和要求。 此时，提供商将提供采用 JSON 格式的数据块。 使用此数据可使提供商和条件访问一起服务于租户，创建新控件，并确定条件访问如何判断用户是否通过提供商成功执行了验证。

复制 JSON 数据，然后将其粘贴到相关文本框中。 不要对 JSON 做任何更改，除非用户明确理解所做的更改。 做出任何更改可能中断提供商和 Microsoft 之间的联系，并且有可能将你和你的用户锁定在帐户之外。

创建自定义控件的选项位于“条件访问”  页的“管理”  部分中。

![条件访问中的自定义控件接口](./media/controls/custom-controls-conditional-access.png)

单击“新建自定义控件”  ，打开包含控件 JSON 数据文本框的边栏选项卡。  

![新建自定义控件](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>删除自定义控件

若要删除自定义控件，必须先确定它未在任何条件访问策略中使用。 完成后：

1. 转到“自定义控件”列表
1. 单击...  
1. 选择“删除”  。

## <a name="editing-custom-controls"></a>编辑自定义控件

若要编辑自定义控件，必须删除当前控件，然后使用更新的信息创建新控件。

## <a name="known-limitations"></a>已知的限制

在 Intune 设备注册过程中或者在将设备加入 Azure AD 时，不能将自定义控件与 Identity Protection 的需要 Azure AD 多重身份验证的自动化以及要求多重身份验证声明的 Azure AD 自助式密码重置 (SSPR) 配合使用来提升 Privileged Identity Manager (PIM) 中角色的权限。

## <a name="next-steps"></a>后续步骤

- [条件访问常见策略](concept-conditional-access-policy-common.md)

- [“仅报表”模式](concept-conditional-access-report-only.md)

- [使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
