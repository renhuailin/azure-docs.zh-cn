---
title: 启用 Azure Active Directory B2C 中的年龄限制 | Microsoft Docs
description: 学习如何辨别使用应用程序的未成年人。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f0502f11c9a6e900ae8f25209b6897f8384c2773
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777754"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>启用 Azure Active Directory B2C 中的年龄限制

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

借助 Azure Active Directory B2C (Azure AD B2C) 中的年龄限制，可以辨别在经得或未经得其家长同意的情况下想要使用你的应用程序的未成年人。 可以选择阻止未成年人登录到应用程序。 或者允许用户完成登录，并在应用程序中提供未成年人状态。 

>[!IMPORTANT]
>此功能目前以公共预览版提供。 请勿将此功能用于生产应用程序。
>

为用户流启用年龄限制后，应用程序将要求用户提供其出生日期和居住地所在国家/地区。 如果用户登录之前未输入信息，则他们需要在下次登录时输入该信息。 每次用户登录时都会应用这些规则。

![年龄限制信息收集流的屏幕截图](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C 使用用户输入的信息来确定他们是否是未成年人。 然后，在其帐户中更新“ageGroup”字段。 值可为 `null`、`Undefined`、`Minor`、`Adult` 和 `NotAdult`。  然后，将 ageGroup 和 consentProvidedForMinor 字段用于计算 legalAgeGroupClassification 的值。


## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>设置租户年龄限制

若要在用户流中使用年龄限制，需要将租户配置为具有附加属性。

1. 使用[此链接](https://portal.azure.com/?Microsoft_AAD_B2CAdmin_agegatingenabled=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)可试用年龄限制（预览版）。
1. 在门户工具栏中选择“目录 + 订阅”图标，确保使用包含 Azure AD B2C 租户的目录。
1. 在“门户设置 | 目录+订阅”页上，在“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 Azure AD B2C。
1. 在左侧菜单中选择租户的“属性”。
1. 在“年龄限制”下，选择“配置”。 
1. 等待操作完成，系统将设置租户的年龄限制。

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>在用户流中启用年龄限制

将租户设置为使用年龄限制后，可在启用它的[用户流](user-flow-versions.md)中使用此功能。 通过执行以下步骤启用“年龄限制”：

1. 创建启用了年龄限制的用户流。
1. 创建用户流后，在菜单中选择“属性”。
1. 在“年龄限制”部分，选择“已启用”。
1. 对于“注册或登录”，请选择用户管理方式：
    - 允许未成年人访问你的应用程序。
    - 仅阻止未满须经得家长同意的年龄的未成年人访问应用程序。
    - 阻止所有未成年人访问应用程序。
1. 对于“阻止时”，请选择以下选项之一：
    - **将 JSON 发送回应用程序** - 此选项会将响应发送回到阻止了未成年人的应用程序。
    - **显示错误页** - 向用户显示一个页面，告知他们不能访问该应用程序。

## <a name="test-your-user-flow"></a>测试用户流

1. 若要测试策略，请选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为“testapp1”的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”按钮。
1. 使用本地帐户或社交帐户登录。 然后选择你的居住地所在国家/地区，以及与未成年人相仿的出生日期。 
1. 重复该测试，并选择一个与成年人相仿的出生日期。  

以未成年人身份登录时，应会看到以下错误消息：“很遗憾，已阻止你登录。你所在国家/地区的隐私和网络安全法禁止访问属于儿童的帐户。”

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>在自定义策略中启用年龄限制

1. 在 [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies) 中获取年龄限制策略的示例。
1. 在每个文件中，将字符串 `yourtenant` 替换为 Azure AD B2C 租户的名称。 例如，如果 B2C 租户的名称为 contosob2c，则 `yourtenant.onmicrosoft.com` 的所有实例都将变为 `contosob2c.onmicrosoft.com`。
1. 上传策略文件。

::: zone-end

## <a name="next-steps"></a>后续步骤

- 了解如何[在 Azure AD B2C 中管理用户访问](manage-user-access.md)。
