---
author: erhopf
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: c2cadc349c24bb59e6ab97a89a8f5182a9b96632
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778157"
---
## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 为 Azure Active Directory 身份验证配置的沉浸式阅读器资源。 按照[这些说明](../../how-to-create-immersive-reader.md)进行设置。  在配置环境属性时，将需要在此处创建的一些值。 将会话的输出保存到文本文件中，以供将来参考。
* [Git](https://git-scm.com/)。
* [沉浸式阅读器 SDK](https://github.com/microsoft/immersive-reader-sdk)。
* [Android Studio](https://developer.android.com/studio)。

## <a name="configure-authentication-credentials"></a>配置身份验证凭据

1. 启动 Android Studio 并从“immersive-reader-sdk/js/samples/quickstart-java-android”目录 (Java) 或“immersive-reader-sdk/js/samples/quickstart-kotlin”目录 (Kotlin) 中打开项目。

1. 在 /assets 文件夹内创建名为“env”的文件。 添加以下名称和值，并根据需要提供值。 不要将此文件提交到源代码管理中，因为它包含不应公开的机密。
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>启动沉浸式阅读器以显示示例内容

从 AVD 管理器中选择设备模拟器并运行该项目。

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](../../reference.md)。
* 在 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) 上查看代码示例。