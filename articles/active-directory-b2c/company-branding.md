---
title: 将品牌添加到组织的登录页面
titleSuffix: Azure AD B2C
description: 了解如何将组织的品牌添加到 Azure Active Directory B2C 页面。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111194"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>将品牌添加到组织的 Azure Active Directory B2C 页面

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

您可以通过使用 Azure Active Directory [公司品牌](../active-directory/fundamentals/customize-branding.md)，使用横幅徽标、背景图像和背景色自定义 Azure AD B2C 页面。 公司品牌包括注册、登录、配置文件编辑和密码重置。 

> [!TIP]
> 若要自定义除横幅徽标、背景图像或背景色以外的用户流页面的其他方面，请参阅如何 [通过 HTML 模板自定义 UI](customize-ui-with-html.md)。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


若要自定义用户流页面，请先在 Azure Active Directory 中配置公司品牌，然后在 Azure AD B2C 中用户流的页面布局中启用它。

## <a name="configure-company-branding"></a>配置公司品牌

首先在 **公司品牌** 内设置横幅徽标、背景图像和背景色。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在 " **管理**" 下，选择 " **公司品牌**"。
1. 按照 [将品牌添加到组织的 Azure Active Directory 登录页](../active-directory/fundamentals/customize-branding.md)中的步骤进行操作。

在 Azure AD B2C 中配置公司品牌时，请牢记以下事项：

* Azure AD B2C 中的公司品牌目前仅限于 **背景图像**、 **横幅徽标** 和 **背景色** 自定义。 *不支持*"公司品牌" 窗格中的其他属性，例如 "**高级设置**" 中的属性。
* 在用户流页面中，在加载背景图像之前显示背景色。 建议选择与背景图像中的颜色最接近的背景色，以获得更流畅的加载体验。
* 当用户启动注册用户流时，会向用户发送的验证电子邮件中显示横幅徽标。

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>在用户流页面中启用署名

配置公司品牌后，请在用户流中启用它。

1. 在 Azure 门户的左侧菜单中，选择 " **Azure AD B2C**"。
1. 在“策略”下，选择“用户流(策略)”。 
1. 选择要为其启用公司品牌的用户流。 标准 *登录* 和标准 *配置文件编辑* 用户流类型 **不支持** 公司品牌。
1. 在 " **自定义**" 下，选择 " **页面布局**"，然后选择想要品牌的布局。 例如，选择 " **统一注册" 或 "登录" 页**。
1. 对于 " **页面布局版本 (预览")**，选择 "版本 **1.2.0** " 或更高版本。
1. 选择“保存”。

如果要在用户流中标记所有页面，请在用户流中为每个页面布局设置页面布局版本。

![页面布局选择 Azure AD B2C 的 Azure 门户](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>选择页面布局

若要启用公司品牌，需要为 [](contentdefinitions.md#migrating-to-page-layout) `contract` 自定义策略中的 *所有* 内容定义定义页面布局版本和页面版本。 值的格式必须包含单词 `contract`：_urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_。 指定使用旧 **DataUri** 值的自定义策略中的页面布局。

了解如何使用页面版本[迁移到页面布局](contentdefinitions.md#migrating-to-page-layout)。

以下示例显示了内容定义标识符以及对应的包含页面协定的 DataUri： 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

以下示例显示了使用海蓝色模板的 " *注册" 和 "登录* 用户" 流页上的自定义横幅徽标和背景图像：

![Azure AD B2C 提供的品牌注册/登录页面](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>在自定义 HTML 中使用公司品牌资产

若要在 [自定义 HTML](customize-ui-with-html.md)中使用公司品牌资产，请在标记外添加以下标记 `<div id="api">` ：

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

图像源被替换为背景图像和横幅徽标的图像源。

## <a name="next-steps"></a>后续步骤

若要详细了解如何自定义应用程序的用户界面，请在 [Azure Active Directory B2C 中自定义应用程序的用户界面](customize-ui-with-html.md)。