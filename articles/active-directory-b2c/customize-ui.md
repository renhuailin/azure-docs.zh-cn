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
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055637"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>自定义 Azure Active Directory B2C 中的用户界面

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

标记和自定义用户界面，该界面 Azure Active Directory B2C 向客户显示的 (Azure AD B2C) ，有助于在应用程序中提供无缝的用户体验。 这些体验包括注册、登录、个人资料编辑和密码重置。 本文介绍如何使用页面模板和公司品牌自定义 Azure AD B2C 页面。 

> [!TIP]
> 若要自定义除页面模板、横幅徽标、背景图像或背景色以外的用户流页面的其他方面，请参阅如何 [通过 HTML 模板自定义 UI](customize-ui-with-html.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>概述

Azure AD B2C 提供了多个内置模板，你可以从中进行选择，为你的用户体验页面提供专业的外观。 这些页面模板还可以使用 [公司品牌](#company-branding) 功能，作为你自己的自定义的起点。

### <a name="ocean-blue"></a>海蓝

在注册登录页上呈现的海蓝色模板的示例：

![海蓝色模板屏幕截图](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>青灰

在注册登录页上呈现的石板灰色模板的示例：

![石板灰色模板屏幕截图](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>经典

在注册登录页上呈现的经典模板的示例：

![经典模板屏幕截图](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>公司品牌

您可以通过使用 Azure Active Directory [公司品牌](../active-directory/fundamentals/customize-branding.md)，使用横幅徽标、背景图像和背景色自定义 Azure AD B2C 页面。 公司品牌包括注册、登录、配置文件编辑和密码重置。 

下面的示例演示了使用海蓝色模板的 " *注册" 和 "登录* " 页，其中包含自定义徽标和背景图像：

![Azure AD B2C 提供的品牌注册/登录页面](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>选择页面模板

::: zone pivot="b2c-user-flow"

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 选择“用户流”。
1. 选择要自定义的用户流。
1. 在左侧菜单中的 " **自定义** " 下，选择 " **页面布局** "，然后选择一个 **模板**。
    ![Azure 门户的用户流页中的模板选择下拉列表](./media/customize-ui/select-page-template.png)

选择模板时，所选模板将应用于用户流中的所有页面。 每个页面的 URI 在 " **自定义页 uri** " 字段中可见。

::: zone-end

::: zone pivot="b2c-custom-policy"

若要选择页面模板，请设置 `LoadUri` [内容定义](contentdefinitions.md)的元素。 下面的示例演示内容定义标识符和相应的 `LoadUri` 。 

海蓝色：

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

石板灰色：

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

若要自定义用户流页面，请先在 Azure Active Directory 中配置公司品牌，然后在 Azure AD B2C 中的用户流中启用它。

首先在 **公司品牌** 内设置横幅徽标、背景图像和背景色。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在 " **管理**" 下，选择 " **公司品牌**"。
1. 按照 [将品牌添加到组织的 Azure Active Directory 登录页](../active-directory/fundamentals/customize-branding.md)中的步骤进行操作。

在 Azure AD B2C 中配置公司品牌时，请牢记以下事项：

* Azure AD B2C 中的公司品牌目前仅限于 **背景图像**、 **横幅徽标** 和 **背景色** 自定义。 "公司品牌" 窗格中的其他属性（例如 " **高级设置**"） *不受支持*。
* 在用户流页面中，在加载背景图像之前显示背景色。 建议选择与背景图像中的颜色最接近的背景色，以获得更流畅的加载体验。
* 当用户启动注册用户流时，会向用户发送的验证电子邮件中显示横幅徽标。



## <a name="enable-company-branding-in-user-flow-pages"></a>在用户流页面中启用公司品牌

::: zone pivot="b2c-user-flow"

配置公司品牌后，请在用户流中启用它。

1. 在 Azure 门户的左侧菜单中，选择 " **Azure AD B2C**"。
1. 在“策略”下，选择“用户流(策略)”。 
1. 选择要为其启用公司品牌的用户流。 标准 *登录* 和标准 *配置文件编辑* 用户流类型 **不支持** 公司品牌。
1. 在 " **自定义**" 下，选择 " **页面布局**"，然后选择你想要品牌的页面。 例如，选择 " **统一注册" 或 "登录" 页**。
1. 对于 " **页面布局版本 (预览")**，选择 "版本 **1.2.0** " 或更高版本。
1. 选择“保存”。

如果要在用户流中标记所有页面，请在用户流中为每个页面布局设置页面布局版本。

![页面布局选择 Azure AD B2C 的 Azure 门户](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

配置公司品牌后，请在自定义策略中启用它。 [](contentdefinitions.md#migrating-to-page-layout) `contract` 为自定义策略中的 *所有* 内容定义配置页面布局版本和页面版本。 值的格式必须包含单词 `contract`：_urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_。 指定使用旧 **DataUri** 值的自定义策略中的页面布局。 有关详细信息，请参阅如何在页面版本中 [迁移到页面布局](contentdefinitions.md#migrating-to-page-layout) 。

下面的示例显示了内容定义及其对应的页面协定和 *海蓝* 页面模板： 

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

若要详细了解如何自定义应用程序的用户界面，请在 [Azure Active Directory B2C 中自定义应用程序的用户界面](customize-ui-with-html.md)。
