---
title: 将身份验证令牌传递到媒体服务 v3 | Microsoft Docs
description: 了解如何将身份验证令牌从客户端发送到媒体服务 v3 密钥传送服务
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572222"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>将令牌传递到 Azure 媒体服务 v3 密钥传送服务

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

客户经常会问，播放器如何将令牌传递到 Azure 媒体服务密钥传送服务来进行验证，以便播放器可以获取密钥。 媒体服务支持简单 Web 令牌 (SWT) 和 JSON Web 令牌 (JWT) 格式。 令牌身份验证可应用于任何类型的密钥，不论系统中使用的是通用加密还是高级加密标准 (AES) 信封加密。

 可使用播放器通过以下方式传递令牌，具体取决于设定为目标的播放器和平台：

## <a name="pass-a-token-through-the-http-authorization-header"></a>通过 HTTP 授权标头传递令牌

> [!NOTE]
> 根据 OAuth 2.0 规格，应使用“Bearer”前缀。 若要设置视频源，请选择“AES（JWT 令牌）”或“AES（SWT 令牌）”。 令牌是通过授权标头传递的。

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>通过使用“token=tokenvalue”添加一个 URL 查询参数来传递令牌。

> [!NOTE]
> 不应使用“Bearer”前缀。 由于令牌是通过 URL 发送的，因此需要保护令牌字符串。 下面的 C# 示例代码显示了如何执行此操作：

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>通过 CustomData 字段传递令牌

此选项仅用于 PlayReady 许可证获取，通过 PlayReady 许可证获取质询的 CustomData 字段。 在此情况下，令牌必须位于如下所述的 xml 文档中：

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

将身份验证令牌放入 Token 元素中。
