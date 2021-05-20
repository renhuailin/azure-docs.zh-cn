---
title: 设置配置文件编辑流
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中设置配置文件编辑流。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d84756a2ae4f8897c42e1846e3a91dbb9f7ad7e1
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257035"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置配置文件编辑流

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>配置文件编辑流

配置文件编辑策略使用户可以管理其配置文件属性，如显示名称、姓氏、名字、城市和其他属性。 配置文件编辑流涉及以下步骤： 

1. 使用本地帐户或社交帐户注册或登录。 如果会话仍处于活动状态，Azure AD B2C 会授权用户，并跳到下一步。
1. Azure AD B2C 从目录中读取用户配置文件，并允许用户编辑属性。

![配置文件编辑流](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>必备知识

[在 Azure Active Directory B2C 中注册 Web 应用程序](tutorial-register-applications.md)（如果尚未这样做）。

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>创建配置文件编辑用户流

如果希望用户能够在你的应用程序中编辑其个人资料，请使用个人资料编辑用户流。

1. 在 Azure AD B2C 租户概述页面的菜单中，选择“用户流”，然后选择“新建用户流”。
1. 在“创建用户流”页面上，选择“个人资料编辑”用户流 。 
1. 在“选择版本”下，选择“建议”，然后选择“创建”  。
1. 输入该用户流的 **名称**。 例如 *profileediting1*。
1. 对于“标识提供者”，请选择“电子邮件登录”。
1. 对于“用户属性”，请选择你希望客户能够在其个人资料中编辑的属性。 例如，选择“显示更多”，然后选择“显示名称”和“职务”所对应的属性和声明。 单击 **“确定”** 。
1. 单击“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择已创建的用户流以打开其概览页，然后选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击“运行用户流”，然后使用前面创建的帐户登录。
1. 现在，你可以更改用户的显示名称和职务。 单击 **“继续”** 。 令牌将返回到 `https://jwt.ms` 并显示出来。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>创建配置文件编辑策略

自定义策略是上传到 Azure AD B2C 租户的一组 XML 文件，用于定义用户旅程。 我们提供了初学者包，其中有多个预构建策略，包括注册和登录、密码重置以及配置文件编辑策略。 有关详细信息，请参阅 [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)。

::: zone-end

## <a name="next-steps"></a>后续步骤

* 添加[使用社交标识提供者登录](add-identity-provider.md)。