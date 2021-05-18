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
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8469e05b82a651760829761ca57af3bdb1b256a9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443400"
---
# <a name="page-layout-versions"></a>页面布局版本

页面布局包会定期更新，其中包括页面元素的修复和改进。 以下更改日志详述了在每个版本中引入的更改。

## <a name="jquery-version"></a>jQuery 版本

Azure AD B2C 页面布局使用以下版本的 [jQuery 库](https://jquery.com/)：

|从页面布局版本  |jQuery 版本  |
|---------|---------|
|2.1.4 | 3.5.1 |
|1.2.0 | 3.4.1 |
|1.1.0 | 1.10.2 |

## <a name="self-asserted-page-selfasserted"></a>自断言页面 (selfasserted)

**2.1.4**
- 已将 jQuery 更新至版本 3.5.1。
- 已将 HandlebarJS 版本更新至 4.7.6。

**2.1.3**
- 安全修补程序。

**2.1.2**
- 修复了各种语言（例如西班牙语和法语）的本地化编码问题。

**2.1.1**

- 除了 `intro` 之外，还添加了一个 UXString `heading` 作为标题显示在页面上。 默认情况下会隐藏此项。
- 添加了允许将密码保存到 iCloud 密钥链的支持。
- 添加了允许使用策略或 QueryString 参数 `pageFlavor` 来选择布局（classic、oceanBlue 或 slateGray）的支持。
- 在自断言页面上添加了免责声明。
- 当页面加载时，焦点现在放在第一个可编辑字段上。
- 当有多个字段存在错误时，焦点现在放在第一个错误字段上。
- 验证电子邮件验证码后，焦点现在放在“更改”按钮上。

**2.1.0**

- 本地化和辅助功能修补程序。

**2.0.0**

- 在自定义策略中添加了对[显示控件](display-controls.md)的支持。

**1.2.0**

- “用户名/电子邮件”和“密码”字段现在使用 `form` HTML 元素，以允许 Edge 和 Internet Explorer (IE) 正确保存此信息。
- 添加了可配置的用户输入验证延迟，以改善用户体验。
- 辅助功能修复
- 修复了一个辅助功能问题，错误消息现在将由讲述人朗读。 
- 在验证电子邮件后，焦点现在放在密码字段上。
- 从复选框控件中删除了 `autofocus`。 
- 添加了使用显示控件进行电话号码验证的支持。
- 你现在可以[在 HTML 标记中](customize-ui-with-html.md#guidelines-for-using-custom-page-content)添加 `data-preload="true"` 属性
  - 在加载 HTML 模板的同时加载链接的 CSS 文件，这样它就不会在加载文件之间“闪烁”。
  - 控制在页面加载前提取和执行 `script` 标记的顺序。
- 电子邮件字段现在为 `type=email`，手机键盘会提供正确的建议。
- 对 Chrome 转换的支持。
- 在用户流页面中添加了对公司品牌的支持。

**1.1.0**

- 删除了取消警报
- 错误元素的 CSS 类
- 显示/隐藏错误逻辑得到了改进
- 删除了默认的 CSS

**1.0.0**

- 初始版本

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>带有密码重置链接的统一登录注册页面 (unifiedssp)

> [!TIP]
> 如果对页面进行本地化以支持多种区域设置或用户流中的语言。 [本地化 ID](localization-string-ids.md) 提供了一系列可用于所选页面版本的本地化 ID。

**2.1.4**
- 已将 jQuery 更新至版本 3.5.1。
- 已将 HandlebarJS 版本更新至 4.7.6。

**2.1.3**
- 安全修补程序。
- 小 bug 修复。

**2.1.2**
- 修复了各种语言（例如西班牙语和法语）的本地化编码问题。
- 允许将“忘记密码”链接用作声明交换。 有关详细信息，请参阅[自助式密码重置](add-password-reset-policy.md#self-service-password-reset-recommended)。

**2.1.1**
- 除了 `intro` 之外，还添加了一个 UXString `heading` 作为标题显示在页面上。 默认情况下会隐藏此项。
- 添加了允许使用策略或 QueryString 参数 `pageFlavor` 来选择布局（classic、oceanBlue 或 slateGray）的支持。
- 添加了允许将密码保存到 iCloud 密钥链的支持。
- 当有多个字段存在错误时，焦点现在放在第一个错误字段上。
- 当页面加载时，焦点现在放在第一个可编辑字段上。
- 为声明提供程序选择链接 `bottomUnderFormClaimsProviderSelections` 添加了新位置。
- 删除了不再使用的 UXString。

**2.1.0**

- 添加了对多个注册链接的支持。
- 根据策略中定义的谓词规则添加了对用户输入验证的支持。

**1.2.0**

- “用户名/电子邮件”和“密码”字段现在使用 `form` HTML 元素，以允许 Edge 和 Internet Explorer (IE) 正确保存此信息。
- 辅助功能修复
- 现在可以[在 HTML 标记中](customize-ui-with-html.md#guidelines-for-using-custom-page-content)添加 `data-preload="true"` 特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在加载 HTML 模板的同时加载链接的 CSS 文件，这样它就不会在加载文件之间“闪烁”。
  - 控制在页面加载前提取和执行 `script` 标记的顺序。
- 电子邮件字段现在为 `type=email`，手机键盘会提供正确的建议。
- 对 Chrome 转换的支持。
- 在用户流页面中添加了对租户品牌的支持。

**1.1.0**

- 添加了“使我保持登录状态”(KMSI) 控件

**1.0.0**

- 初始版本

## <a name="mfa-page-multifactor"></a>MFA 页面 (multifactor)

**1.2.4**
- 已将 jQuery 更新至版本 3.5.1。
- 已将 HandlebarJS 版本更新至 4.7.6。

**1.2.3**
- 允许通过语言本地化覆盖工具提示字符串。
- 安全修补程序。
- 小 bug 修复。

**1.2.2**
- 修复了使用 iOS 时与自动填写验证码相关的一个问题。
- 修复了从 Android Webview 将令牌重定向到信赖方时出现的一个问题。 
- 除了 `intro` 之外，还添加了一个 UXString `heading` 作为标题显示在页面上。 默认情况下会隐藏此项。  
- 添加了允许使用策略或 QueryString 参数 `pageFlavor` 来选择布局（classic、oceanBlue 或 slateGray）的支持。

**1.2.1**

- 默认模板上的辅助功能修复

**1.2.0**

- 辅助功能修复
- 现在可以[在 HTML 标记中](customize-ui-with-html.md#guidelines-for-using-custom-page-content)添加 `data-preload="true"` 特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在加载 HTML 模板的同时加载链接的 CSS 文件，这样它就不会在加载文件之间“闪烁”。
  - 控制在页面加载前提取和执行 `script` 标记的顺序。
- 电子邮件字段现在为 `type=email`，移动键盘将提供正确的建议
- 对 Chrome 转换的支持。
- 在用户流页面中添加了对租户品牌的支持。

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

**1.2.1**
- 已将 jQuery 更新至版本 3.5.1。
- 已将 HandlebarJS 版本更新至 4.7.6。

**1.2.0**

- 辅助功能修复
- 现在可以[在 HTML 标记中](customize-ui-with-html.md#guidelines-for-using-custom-page-content)添加 `data-preload="true"` 特性来控制 CSS 和 JavaScript 的加载顺序。
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

**1.2.1**
- 已将 jQuery 更新至版本 3.5.1。
- 已将 HandlebarJS 版本更新至 4.7.6。

**1.2.0**

- 辅助功能修复
- 现在可以[在 HTML 标记中](customize-ui-with-html.md#guidelines-for-using-custom-page-content)添加 `data-preload="true"` 特性来控制 CSS 和 JavaScript 的加载顺序。
  - 在加载 HTML 模板的同时加载链接的 CSS 文件，这样它就不会在加载文件之间“闪烁”。
  - 控制在页面加载前提取和执行 `script` 标记的顺序。
- 电子邮件字段现在为 `type=email`，移动键盘将提供正确的建议
- 对 Chrome 转换的支持

**1.0.0**

- 初始版本

## <a name="next-steps"></a>后续步骤

若要详细了解如何在自定义策略中自定义应用程序的用户界面，请参阅[使用自定义策略自定义应用程序的用户界面](customize-ui-with-html.md)。
