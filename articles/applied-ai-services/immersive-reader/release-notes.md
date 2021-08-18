---
title: 沉浸式阅读器 SDK 发行说明
titleSuffix: Azure Applied AI Services
description: 详细了解沉浸式阅读器 JavaScript SDK 的新增功能。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 833066839b597e52c1aa0e965f959c2c81e4ab85
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778380"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>沉浸式阅读器 JavaScript SDK 发行说明

## <a name="version-110"></a>版本 1.1.0

此版本包含新功能、安全漏洞修补程序、bug 修复、代码示例更新和配置选项。

#### <a name="new-features"></a>新功能

* 能够在不同的浏览器和设备中保存和加载用户首选项
* 能够配置默认显示选项
* 添加用于设置翻译语言的选项，启用单词翻译，在启动沉浸式阅读器时启用文档翻译
* 添加对通过选项配置“大声朗读”的支持
* 添加禁用首次运行体验的功能
* 添加用于 UWP 的 ImmersiveReaderView

#### <a name="improvements"></a>改进

* 将 Android 代码示例 HTML 更新为使用最新的 SDK
* 将启动响应更新为返回处理的字符数
* 将代码示例更新为使用 v1.1.0
* 在已加载时不允许调用 launchAsync
* 通过忽略数据不是字符串的消息，检查无效内容
* 将窗口调用包装到 if 子句，以检查浏览器是否支持承诺

#### <a name="fixes"></a>修复项

* 通过从 gitignore 删除 yarn.lock 修复 dependabot
* 通过将 quickstart-nodejs 代码示例中的 pug 升级到 v3.0.0，修复安全漏洞
* 通过升级 Jest 和 TypeScript 包，修复多个安全漏洞
* 通过将 Microsoft.IdentityModel.Clients.ActiveDirectory 升级到 v 5.2.0 修复安全漏洞

<br>

## <a name="version-100"></a>版本 1.0.0

此版本包含中断性变更、新功能、代码示例改进和 bug 修补程序。

#### <a name="breaking-changes"></a>重大更改

* 需要 Azure AD 令牌和子域，弃用先前版本中使用的令牌。
* 将 CookiePolicy 设置为禁用。 默认情况下禁用用户首选项保留。 如果 CookiePolicy 未设置为启用，则阅读器每次都使用默认设置启动。

#### <a name="new-features"></a>新功能

* 添加对启用或禁用 cookie 的支持
* 添加 Android Kotlin 快速入门代码示例
* 添加 Android Java 快速入门代码示例
* 添加 Node.js 快速入门代码示例

#### <a name="improvements"></a>改进

* 更新 Node.js 高级 README.md
* 将 Python 代码示例从高级更改为快速入门
* 将 iOS Swift 代码示例移到 js/samples
* 将代码示例更新为使用 v1.0.0

#### <a name="fixes"></a>修复项

* 修复 Node.js 高级代码示例
* 添加 advanced-csharp-multiple-resources 缺失的文件
* 从超链接中删除 en-us

<br>

## <a name="version-003"></a>版本 0.0.3

此版本包含新功能、代码示例改进、安全漏洞修复和 bug 修补程序。

#### <a name="new-features"></a>新功能

* 添加 iOS Swift 代码示例
* 添加 C# 高级代码示例来演示如何使用多个资源 
* 添加对禁用全屏切换功能的支持
* 添加对隐藏沉浸式阅读器应用程序退出按钮的支持
* 添加主机应用程序在退出沉浸式阅读器时可能会使用的回调函数
* 将代码示例更新为使用 Azure Active Directory 身份验证

#### <a name="improvements"></a>改进

* 将 C# 高级代码示例更新为包含 Word 文档
* 将代码示例更新为使用 v0.0.3

#### <a name="fixes"></a>修复项

* 将 lodash 升级到版本 4.17.14 以修复安全漏洞
* 更新 C# MSAL 库以修复安全漏洞
* 将 mixin-deep 升级到版本 1.3.2 以修复安全漏洞
* 升级使用易受攻击的 set-value 和 mixin-deep 版本的 jest、webpack 和 webpack-cli，以修复安全漏洞

<br>

## <a name="version-002"></a>版本 0.0.2

此版本包含新功能、代码示例改进、安全漏洞修复和 bug 修补程序。

#### <a name="new-features"></a>新功能

* 添加 Python 高级代码示例
* 添加 Java 快速入门代码示例
* 添加简单的代码示例

#### <a name="improvements"></a>改进

* 将 resourceName 重命名为 cogSvcsSubdomain
* 从代码中移除机密，然后使用环境变量
* 将代码示例更新为使用 v0.0.2

#### <a name="fixes"></a>修复项

* 修复沉浸式阅读器辅助功能按钮 bug
* 修复滚动中断问题
* 将 handlebars 包升级到版本 4.1.2 以修复安全漏洞
* 修复 SDK 单元测试中的 bug
* 修复 JavaScript Internet Explorer 11 兼容性 bug
* 更新 SDK URL

<br>

## <a name="version-001"></a>版本 0.0.1

沉浸式阅读器 JavaScript SDK 的初始版本。

* 添加沉浸式阅读器 JavaScript SDK
* 添加对指定 UI 语言的支持
* 添加超时功能，用于决定 launchAsync 函数何时失败并引发超时错误
* 添加对指定沉浸式阅读器 iframe 的 Z-索引的支持
* 添加对使用 Web 视图标签来代替 iframe 的支持，以与 Chrome 应用兼容
* 添加 SDK 单元测试
* 添加 Node.js 高级代码示例
* 添加 C# 高级代码示例
* 添加 C# 快速入门代码示例
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
