---
title: 设置密码重置流
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中设置密码重置流。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618700"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置密码重置流

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>密码 rest 流

密码重置策略允许用户重置其自己的忘记密码。 密码重置流涉及以下步骤： 
1. 在注册和登录页上，用户单击 "忘记了密码？" 帐户。 Azure AD B2C 将向应用程序返回 AADB2C90118 错误代码。 应用通过调用密码重置策略来处理此错误代码。 
1. 用户通过定时一次性密码提供并验证其电子邮件。
1. 输入新密码。

![密码重置流](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>先决条件

如果尚未这样做，请 [在 Azure Active Directory B2C 中注册 web 应用程序](tutorial-register-applications.md)。

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>创建密码重置用户流

若要允许应用程序用户重置其密码，请使用密码重置用户流。

1. 在 Azure AD B2C 租户概述菜单中，选择“用户流”，然后选择“新建用户流”。
1. 在“创建用户流”页面上，选择“密码重置”用户流 。 
1. 在“选择版本”下，选择“建议”，然后选择“创建”  。
1. 输入该用户流的 **名称**。 例如 *passwordreset1*。
1. 对于“标识提供者”，请启用“使用电子邮件地址重置密码”。
2. 在“应用程序声明”下单击“显示更多”，并选择你希望在发回到应用程序的授权令牌中返回的声明。 例如，选择“用户的对象 ID”。
3. 单击 **“确定”** 。
4. 单击“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择已创建的用户流以打开其概览页，然后选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击“运行用户流”，验证之前创建的帐户的电子邮件地址，然后选择“继续”。
1. 现在可以更改用户的密码。 更改密码，然后选择“继续”。 令牌将返回到 `https://jwt.ms` 并显示出来。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>创建密码重置策略

自定义策略是上传到 Azure AD B2C 租户的一组 XML 文件，用于定义用户旅程。 我们提供了包含若干预先构建的策略的初学者包，其中包括：注册和登录、密码重置和配置文件编辑策略。 有关详细信息，请参阅 [Azure AD B2C 中的自定义策略入门](custom-policy-get-started.md)。

::: zone-end

## <a name="next-steps"></a>后续步骤

设置 [配置文件编辑流](add-profile-editing-policy.md)。