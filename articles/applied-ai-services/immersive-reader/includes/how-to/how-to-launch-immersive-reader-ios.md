---
author: erhopf
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 90344270938c778d2a264292049d723810481c26
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778158"
---
## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 为 Azure Active Directory 身份验证配置的沉浸式阅读器资源。 按照[这些说明](../../how-to-create-immersive-reader.md)进行设置。  在配置环境属性时，将需要在此处创建的一些值。 将会话的输出保存到文本文件中，以供将来参考。
* [macOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/)。
* [沉浸式阅读器 SDK](https://github.com/microsoft/immersive-reader-sdk)。
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>配置身份验证凭据

1. 打开 Xcode 并打开 immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj。
1. 在顶部菜单中，选择“产品” > “方案” > “编辑方案”  。
1. 在“运行”视图中，选择“参数”选项卡。
1. 在“环境变量”部分中，添加以下名称和值。 提供在创建沉浸式阅读器资源时给出的值。

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

不要将此更改提交到源代码管理中，因为它包含不应公开的机密。

## <a name="start-the-immersive-reader-with-sample-content"></a>启动沉浸式阅读器以显示示例内容

在 Xcode 中，选择“Ctrl+R”运行项目。

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](../../reference.md)。
* 在 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) 上查看代码示例。
