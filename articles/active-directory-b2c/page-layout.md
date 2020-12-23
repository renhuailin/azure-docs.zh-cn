---
title: 页面布局版本
titleSuffix: Azure AD B2C
description: 有关自定义策略中 UI 自定义的页面布局版本历史记录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: beb38be985457ea36b2cea9a6dc337ba305d503f
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108495"
---
# <a name="page-layout-versions"></a>页面布局版本

页面布局包会定期更新，其中包括页面元素的修复和改进。 以下更改日志详述了在每个版本中引入的更改。

## <a name="self-asserted-page-selfasserted"></a>自断言页面 (selfasserted)

**2.1.1**

- 除了之外，还添加了一个 UXString `heading` `intro` ，以作为标题显示在该页上。 默认情况下隐藏此值。
- 添加了对将密码保存到 iCloud 密钥链的支持。
- 添加了对使用 policy 或 QueryString 参数 `pageFlavor` 选择布局 (经典、oceanBlue 或石板灰) 的支持。
- 添加了自断言页上的免责声明。
- 当页面加载时，焦点现在置于第一个可编辑字段。
- 如果有多个字段出现错误，焦点将置于第一个错误字段。
- 验证电子邮件验证码后，焦点会置于 "更改" 按钮上。

**2.1.0**

- 本地化和辅助功能修补程序。

**2.0.0**

- 在自定义策略中添加了对[显示控件](display-controls.md)的支持。

**1.2.0**

- “用户名/电子邮件”和“密码”字段现在使用 `form` HTML 元素，以允许 Edge 和 Internet Explorer (IE) 正确保存此信息。
- 添加了可配置的用户输入验证延迟，以改善用户体验。
- 辅助功能修复
- 修复了一个可访问性问题，使讲述人现在可以阅读错误消息。 
- 电子邮件经过验证后，焦点会置于密码字段。
- `autofocus`从 checkbox 控件中删除。 
- 添加了对电话号码验证的显示控件的支持。
- 现在，你可以 `data-preload="true"` 在 html 标记中添加特性 [在 html 标记] 中 (自定义-ui 的
  - 在加载 HTML 模板的同时加载链接的 CSS 文件，这样它就不会在加载文件之间“闪烁”。
  - 控制在页面加载前提取和执行 `script` 标记的顺序。
- 现在，电子邮件字段 `type=email` 将提供正确的建议。
- 支持 Chrome 翻译。
- 添加了对用户流页面中公司品牌的支持。

**1.1.0**

- 删除了取消警报
- 错误元素的 CSS 类
- 显示/隐藏错误逻辑得到了改进
- 删除了默认的 CSS

**1.0.0**

- 初始版本

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>带有密码重置链接的统一登录注册页面 (unifiedssp)

**2.1.1**
- 除了之外，还添加了一个 UXString `heading` `intro` ，以作为标题显示在该页上。 默认情况下隐藏此值。
- 添加了对使用 policy 或 QueryString 参数 `pageFlavor` 选择布局 (经典、oceanBlue 或石板灰) 的支持。
- 添加了对将密码保存到 iCloud 密钥链的支持。
- 如果有多个字段出现错误，焦点将置于第一个错误字段。
- 当页面加载时，焦点现在置于第一个可编辑字段。
- 为声明提供程序选择链接添加了一个新位置 `bottomUnderFormClaimsProviderSelections` 。
- 删除不再使用的 UXStrings。

**2.1.0**

- 添加了对多个注册链接的支持。
- 根据策略中定义的谓词规则添加了对用户输入验证的支持。

**1.2.0**

- “用户名/电子邮件”和“密码”字段现在使用 `form` HTML 元素，以允许 Edge 和 Internet Explorer (IE) 正确保存此信息。
- 辅助功能修复
- 你现在可以 `data-preload="true"` [在 HTML 标记中](customize-ui-with-html.md#guidelines-for-using-custom-page-content) 添加特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在加载 HTML 模板的同时加载链接的 CSS 文件，这样它就不会在加载文件之间“闪烁”。
  - 控制在页面加载前提取和执行 `script` 标记的顺序。
- 现在，电子邮件字段 `type=email` 将提供正确的建议。
- 支持 Chrome 翻译。
- 在用户流页中添加了对租户品牌的支持。

**1.1.0**

- 添加了“使我保持登录状态”(KMSI) 控件

**1.0.0**

- 初始版本

## <a name="mfa-page-multifactor"></a>MFA 页面 (multifactor)

**1.2.2**
- 修复了使用 iOS 时自动填充验证代码的问题。
- 修复了从 Android Web 视图将令牌重定向到信赖方的问题。 
- 除了之外，还添加了一个 UXString `heading` `intro` ，以作为标题显示在该页上。 默认情况下隐藏此值。  
- 添加了对使用 policy 或 QueryString 参数 `pageFlavor` 选择布局 (经典、oceanBlue 或石板灰) 的支持。

**1.2.1**

- 默认模板上的辅助功能修复

**1.2.0**

- 辅助功能修复
- 你现在可以 `data-preload="true"` [在 HTML 标记中](customize-ui-with-html.md#guidelines-for-using-custom-page-content) 添加特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在加载 HTML 模板的同时加载链接的 CSS 文件，这样它就不会在加载文件之间“闪烁”。
  - 控制在页面加载前提取和执行 `script` 标记的顺序。
- 电子邮件字段现在为 `type=email`，移动键盘将提供正确的建议
- 支持 Chrome 翻译。
- 在用户流页中添加了对租户品牌的支持。

**1.1.0**

- 删除了“确认代码”按钮
- 代码的输入字段现在只接受最多六 (6) 个字符的输入
- 当用户输入 6 位代码时，该页面会自动尝试验证输入的代码，不需用户单击任何按钮
- 如果代码错误，则会自动清除输入字段
- 如果三 (3) 次尝试都输入了不正确的代码，B2C 会将一个错误发送回信赖方
- 辅助功能修复
- 删除了默认的 CSS

**1.0.0**

- 初始版本

## <a name="exception-page-globalexception"></a>异常页 (globalexception)

**1.2.0**

- 辅助功能修复
- 你现在可以 `data-preload="true"` [在 HTML 标记中](customize-ui-with-html.md#guidelines-for-using-custom-page-content) 添加特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在加载 HTML 模板的同时加载链接的 CSS 文件，这样它就不会在加载文件之间“闪烁”。
  - 控制在页面加载前提取和执行 `script` 标记的顺序。
- 电子邮件字段现在为 `type=email`，移动键盘将提供正确的建议
- 对 Chrome 转换的支持

**1.1.0**

- 辅助功能修复
- 删除了当策略中没有协定时会出现的默认消息
- 删除了默认的 CSS

**1.0.0**

- 初始版本

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>其他页（ProviderSelection、ClaimsConsent、UnifiedSSD）

**1.2.0**

- 辅助功能修复
- 你现在可以 `data-preload="true"` [在 HTML 标记中](customize-ui-with-html.md#guidelines-for-using-custom-page-content) 添加特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在加载 HTML 模板的同时加载链接的 CSS 文件，这样它就不会在加载文件之间“闪烁”。
  - 控制在页面加载前提取和执行 `script` 标记的顺序。
- 电子邮件字段现在为 `type=email`，移动键盘将提供正确的建议
- 对 Chrome 转换的支持

**1.0.0**

- 初始版本

## <a name="next-steps"></a>后续步骤

有关如何在自定义策略中自定义应用程序的用户界面的详细信息，请参阅 [使用自定义策略自定义应用程序的用户界面](customize-ui-with-html.md)。
