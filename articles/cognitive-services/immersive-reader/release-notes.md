---
title: 沉浸式读者 SDK 发行说明
titleSuffix: Azure Cognitive Services
description: 详细了解沉浸式读者 JavaScript SDK 的新增功能。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133482"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>沉浸式读者 JavaScript SDK 发行说明

## <a name="version-110"></a>版本 1.1.0

此版本包含新功能、安全漏洞修补程序、bug 修复、代码示例更新和配置选项。

#### <a name="new-features"></a>新功能

* 在不同的浏览器和设备中启用保存和加载用户首选项
* 启用配置默认显示选项
* 添加选项以设置翻译语言，启用 word 翻译，并在启动沉浸式阅读器时启用文档翻译
* 添加对通过选项配置读取的支持
* 添加禁用首次运行体验的功能
* 为 UWP 添加 ImmersiveReaderView

#### <a name="improvements"></a>改进

* 更新 Android 代码示例 HTML 以使用最新的 SDK
* 更新启动响应以返回处理的字符数
* 更新代码示例以使用 v 1.1。0
* 在已加载时不允许调用 launchAsync
* 通过忽略数据不是字符串的消息来检查无效内容
* 在 if 子句中对窗口进行换行调用以检查是否有承诺的浏览器支持

#### <a name="fixes"></a>修复项

* 通过从 .gitignore 中删除 yarn 来修复 dependabot
* 通过将 pug 升级到3.0.0 中的 v 来修复安全漏洞-nodejs 代码示例
* 升级 Jest 和 TypeScript 包，修复多个安全漏洞
* 通过将 System.identitymodel 升级到 v 5.2.0 修复安全漏洞

<br>

## <a name="version-100"></a>版本 1.0.0

此版本包含重大更改、新功能、代码示例改进和 bug 修复。

#### <a name="breaking-changes"></a>重大更改

* 需要 Azure AD 令牌和子域，以及在早期版本中使用的弃用令牌。
* 将 CookiePolicy 设置为 disabled。 默认情况下，禁用用户首选项的保留期。 如果 CookiePolicy 设置为 "已启用"，则每次都会用默认设置启动读取器。

#### <a name="new-features"></a>新功能

* 添加支持以启用或禁用 cookie
* 添加 Android Kotlin 快速入门代码示例
* 添加 Android Java 快速入门代码示例
* 添加 Node.js 快速入门代码示例

#### <a name="improvements"></a>改进

* 更新 Node.js 高级 README.md
* 将 Python 代码示例从高级更改为快速入门
* 将 iOS Swift 代码示例移入 js/samples
* 更新代码示例以使用 1.0.0 1.0。0

#### <a name="fixes"></a>修复项

* 修复 Node.js 高级代码示例
* 为高级-csharp 添加缺少的文件-多个资源
* 从超链接中删除 en-us

<br>

## <a name="version-003"></a>版本0.0。3

此版本包含新功能、对代码示例的改进、安全漏洞修复和 bug 修复。

#### <a name="new-features"></a>新功能

* 添加 iOS Swift 代码示例
* 添加 c # 高级代码示例演示如何使用多个资源 
* 添加支持以禁用全屏切换功能
* 添加对隐藏沉浸式读者应用程序退出按钮的支持
* 添加主机应用程序在退出沉浸式读取器后可能使用的回调函数
* 更新代码示例以使用 Azure Active Directory 身份验证

#### <a name="improvements"></a>改进

* 更新 c # 高级代码示例以包含 Word 文档
* 更新代码示例以使用 v 0.0。3

#### <a name="fixes"></a>修复项

* 将 lodash 等升级到版本4.17.14 以修复安全漏洞
* 更新 c # MSAL 库以修复安全漏洞
* Upgrade mixin-深层版本1.3.2 修复安全漏洞
* Upgrade jest、webpack 和 webpack-cli，它们使用有漏洞的设置值和 mixin 深度来修复安全漏洞

<br>

## <a name="version-002"></a>版本0.0。2

此版本包含新功能、对代码示例的改进、安全漏洞修复和 bug 修复。

#### <a name="new-features"></a>新功能

* 添加 Python 高级代码示例
* 添加 Java 快速入门代码示例
* 添加简单代码示例

#### <a name="improvements"></a>改进

* 将其重命名为 cogSvcsSubdomain
* 将机密移出代码并使用环境变量
* 更新代码示例以使用 v 0.0。2

#### <a name="fixes"></a>修复项

* 修复沉浸式读者按钮辅助功能 bug
* 修复中断的滚动
* 将 handlebars 包升级到版本4.1.2 以修复安全漏洞
* 修复 SDK 单元测试中的 bug
* 修补 JavaScript Internet Explorer 11 兼容性错误
* 更新 SDK url

<br>

## <a name="version-001"></a>版本0.0。1

沉浸式读者 JavaScript SDK 的初始版本。

* 添加沉浸式读者 JavaScript SDK
* 添加支持以指定 UI 语言
* 添加超时，以确定 launchAsync 函数应何时失败并出现超时错误
* 添加支持以指定沉浸式读取器 iframe 的 z 索引
* 添加支持以使用 web 视图标记而不是 iframe，以与 Chrome 应用兼容
* 添加 SDK 单元测试
* 添加 Node.js 高级代码示例
* 添加 c # 高级代码示例
* 添加 c # 快速入门代码示例
* 添加包配置、Yarn 和其他生成文件
* 添加 git 配置文件
* 将 README.md 文件添加到代码示例和 SDK
* 添加 MIT 许可证
* 添加参与者说明
* 添加静态图标按钮 SVG 资产

## <a name="next-steps"></a>后续步骤

开始使用沉浸式阅读器：

* 阅读[沉浸式阅读器客户端库参考](./reference.md)
* 了解 [GitHub 上的沉浸式阅读器客户端库](https://github.com/microsoft/immersive-reader-sdk)
