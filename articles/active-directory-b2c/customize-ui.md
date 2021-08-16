---
title: 自定义用户界面
titleSuffix: Azure AD B2C
description: 了解如何自定义使用 Azure Active Directory B2C 的应用程序的用户界面。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/26/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ba24afb329b5d588ab7a71976f56f50eb475b04b
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110575401"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>自定义 Azure Active Directory B2C 中的用户界面

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

对 Azure Active Directory B2C (Azure AD B2C) 向客户展示的用户界面进行品牌化和自定义，有助于在应用中提供无缝的用户体验。 这些体验包括注册、登录、个人资料编辑和密码重置。 本文介绍如何使用页面模板和“公司品牌”功能自定义 Azure AD B2C 页面。 

> [!TIP]
> 若要自定义除页面模板、横幅徽标、背景图像或背景色以外的用户流页面的其他方面，请参阅如何[通过 HTML 模板自定义 UI](customize-ui-with-html.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>概述

Azure AD B2C 提供了多个内置模板，你可以从中进行选择，为用户体验页面提供专业的外观。 这些页面模板还可以作为初始模板用于后续使用[公司品牌](#company-branding)功能对页面进行自定义设计。

> [!NOTE]
> 经典模板支持的浏览器包括当前版本和以前版本的 Internet Explorer、Microsoft Edge、Google Chrome、Mozilla Firefox 和 Safari。 “海蓝”和“石板灰”模板对较旧的浏览器版本（例如 Internet Explorer 11 和 10）的支持可能有限；建议你使用你打算支持的浏览器测试应用程序。

### <a name="ocean-blue"></a>海蓝

注册登录页上呈现的“海蓝”模板示例：

![海蓝色模板屏幕截图](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>青灰

注册登录页面上呈现的“青灰”模板示例：

![石板灰色模板屏幕截图](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>经典

注册登录页面上呈现的“经典”模板示例：

![经典模板屏幕截图](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>公司品牌

可通过 Azure Active Directory [公司品牌](../active-directory/fundamentals/customize-branding.md)功能，使用横幅徽标、背景图像和背景色自定义 Azure AD B2C 页面。 公司品牌操作包括了注册、登录、配置文件编辑和密码重置这些页面。 

以下示例演示了使用海蓝模板的“注册和登录”页面，页面中包含了自定义徽标和背景图像：

![Azure AD B2C 进行品牌化后的注册/登录页面](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>选择页面模板

::: zone pivot="b2c-user-flow"

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 选择“用户流”。
1. 选择要自定义的用户流。
1. 在左侧菜单中的“自定义”下选择“页面布局”，然后选择“模板”。
    ![Azure 门户的用户流页中的模板选择下拉列表](./media/customize-ui/select-page-template.png)

选择模板后，所选的模板将应用到用户流中的所有页面。 每个页面的 URI 显示在“自定义页面 URL”字段中。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要选择页面模板，请设置[内容定义](contentdefinitions.md)的 `LoadUri` 元素。 以下示例显示了内容定义标识符以及对应的 `LoadUri`。 

海蓝：

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

青灰：

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

经典： 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>配置公司品牌

若要自定义用户流页面，请先在 Azure Active Directory 中配置“公司品牌”，然后在 Azure AD B2C 中的用户流中启用它。

首先在“公司品牌”内设置横幅徽标、背景图像和背景色。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“管理”下，选择“公司品牌”。
1. 操作[将品牌化功能添加到组织的 Azure Active Directory 登录页面](../active-directory/fundamentals/customize-branding.md)中的步骤。

在 Azure AD B2C 中配置公司品牌时，请牢记以下事项：

* Azure AD B2C 中的“公司品牌”目前仅限于“背景图像”、“横幅徽标”和“背景色”自定义。 不支持“公司品牌”窗格中的其他属性（例如“高级设置”）。
* 在用户流页面中，加载背景图像之前显示背景色。 建议选择与背景图像中的颜色最接近的背景色，以更流畅地进行加载。
* 当用户启动注册用户流时，向用户发送的验证电子邮件中会显示横幅徽标。



## <a name="enable-company-branding-in-user-flow-pages"></a>在用户流页面中启用“公司品牌”

::: zone pivot="b2c-user-flow"

配置“公司品牌”后，在用户流中启用它。

1. 在 Azure 门户的左侧菜单中，选择“Azure AD B2C”。
1. 在“策略”下，选择“用户流(策略)”。 
1. 选择需要启用“公司品牌”的用户流。 标准“登录”和标准“配置文件编辑”用户流类型不支持“公司品牌”功能。
1. 在“自定义”下，选择“页面布局”，然后选择你想要进行品牌化的页面。 例如，选择“统一注册或登录”页。
1. 对于页面布局版本（预览），选择版本 1.2.0或更高版本。
1. 选择“保存”。

如果要对用户流中所有页面进行品牌化，请在用户流中为每个页面布局设置页面布局版本。

![Azure 门户中的 Azure AD B2C 页面布局选择](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

配置“公司品牌”后，在自定义策略中启用它。 为自定义策略中的所有内容定义配置[页面布局版本](contentdefinitions.md#migrating-to-page-layout)和页面`contract`版本。 值的格式必须包含单词 `contract`：_urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_。 在使用旧 DataUri 值的自定义策略中指定页面布局。 有关详细信息，请参阅如何根据页面版本[迁移到页面布局](contentdefinitions.md#migrating-to-page-layout)。

以下示例显示了内容定义及其对应的页面合同和“海蓝”页面模板： 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>后续步骤

有关如何自定义应用程序用户界面的详细信息，请参阅[在 Azure Active Directory B2C 中自定义应用程序的用户界面](customize-ui-with-html.md)。
