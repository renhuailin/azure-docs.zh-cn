---
title: 如何使用 Media Encoder Standard 创建覆盖
description: 了解如何使用 Media Encoder Standard 创建覆盖。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 69930e06e2ce7f2679feec74ca8ccbc93bdb8d30
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101721117"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>如何使用 Media Encoder Standard 创建覆盖

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用 Media Encoder Standard 可以将图像、音频文件或其他视频覆盖到另一个视频。 输入必须仅指定一个文件。 可以指定 JPG、PNG、GIF 或 BMP 格式的图像文件，或者指定音频文件（如 WAV、MP3、WMA 或 M4A 文件）或视频文件。


## <a name="prerequisites"></a>先决条件

* 在示例中收集配置 appsettings.json 文件所需的帐户信息。 如果你不确定怎么做，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](../../active-directory/develop/quickstart-register-app.md)。 Appsettings.json 文件中应有以下值。

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

如果你不熟悉“转换”，建议完成以下活动：

* 阅读[使用媒体服务编码视频和音频](encoding-concept.md)
* 阅读[如何对自定义转换进行编码 - .NET](customize-encoder-presets-how-to.md)。 按照那篇文章中的步骤来设置处理转换所需的 .NET，然后返回此处尝试一个覆盖预设示例。
* 请参阅[转换参考文档](/rest/api/media/transforms)。

熟悉了转换后，请下载覆盖示例。

## <a name="overlays-preset-sample"></a>覆盖预设示例

下载[媒体服务覆盖的示例](https://github.com/Azure-Samples/media-services-overlays)开始进行覆盖。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
