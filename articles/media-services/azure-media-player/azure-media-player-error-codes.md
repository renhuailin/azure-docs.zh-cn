---
title: Azure Media Player 错误代码
description: Azure Media Player 的错误代码参考。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/05/2021
ms.openlocfilehash: 77bd59782d12d7d73e6e882e929ae9a4c95250a7
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449879"
---
# <a name="error-codes"></a>错误代码 #

当播放无法启动或已停止时，将触发一个错误事件并且 `error()` 函数将返回代码和可帮助应用开发人员获取更多详细信息的一条可选消息。 `error().message` 不是向用户显示的消息。  向用户显示的消息基于 `error().code` 位 27-20，请参阅下表。

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>错误代码，位 [31-28]（4 位） ##

描述错误的区域。

- 0 - 未知
- 1 - AMP
- 2 - AzureHtml5JS
- 3 - FlashSS
- 4 - SilverlightSS
- 5 - Html5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>错误代码，位 [27-0]（28 位） ##

描述错误的详细信息，位 27-20 提供高级别，位 19-0 提供更多详细信息（如果可用）。


| amp.errorCode.[name] | 代码，位 [27-0]（28 位） | 说明 |
|---|---:|---|
| MEDIA_ERR_ABORTED 错误范围 (0x0100000 - 0x01FFFFF) | | |
| abortedErrUnknown | 0x0100000 | 通用中止错误 |
| abortedErrNotImplemented | 0x0100001 | 中止错误，未实现 |
| abortedErrHttpMixedContentBlocked | 0x0100002 | 中止错误，阻止了混合内容 - 通常在从 `http://` 页面加载 `https://` 流时出现这种情况 |
| MEDIA_ERR_NETWORK 错误起始值 (0x0200000 - 0x02FFFFF) | | |
| networkErrUnknown | 0x0200000 | 通用网络错误 |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400 错误响应 |
| networkErrHttpUserAuthRequired | 0x0200191 | Http 401 错误响应 |
| networkErrHttpUserForbidden | 0x0200193 | Http 403 错误响应 |
| networkErrHttpUrlNotFound | 0x0200194 | Http 404 错误响应 |
| networkErrHttpNotAllowed | 0x0200195 | Http 405 错误响应 |
| networkErrHttpGone | 0x020019A | Http 410 错误响应 |
| networkErrHttpPreconditionFailed | 0x020019C | Http 412 错误响应 |
| networkErrHttpInternalServerFailure | 0x02001F4 | Http 500 错误响应
| networkErrHttpBadGateway | 0x02001F6 | Http 502 错误响应 |
| networkErrHttpServiceUnavailable | 0x02001F7 | Http 503 错误响应 |
| networkErrHttpGatewayTimeout | 0x02001F8 | Http 504 错误响应 |
| networkErrTimeout | 0x0200258 | 网络超时错误
| networkErrErr | 0x0200259 | 网络连接错误响应 |
| MEDIA_ERR_DECODE 错误 (0x0300000 - 0x03FFFFF) | | |
| decodeErrUnknown | 0x0300000 | 通用解码错误 |
| MEDIA_ERR_SRC_NOT_SUPPORTED 错误 (0x0400000 - 0x04FFFFF) | | |
| srcErrUnknown | 0x0400000 | 不支持通用来源错误 |
| srcErrParsePresentation | 0x0400001 | 演示分析错误 |
| srcErrParseSegment | 0x0400002 | 段分析错误 |
| srcErrUnsupportedPresentation | 0x0400003 | 不支持演示 |
| srcErrInvalidSegment | 0x0400004 | 段无效 |
| MEDIA_ERR_ENCRYPTED 错误起始值 (0x0500000 - 0x05FFFFF) | | |
| encryptErrUnknown | 0x0500000 | 通用加密错误 | 
| encryptErrDecrypterNotFound | 0x0500001 | 找不到解密器 |
| encryptErrDecrypterInit | 0x0500002 | 解密器初始化错误 |
| encryptErrDecrypterNotSupported | 0x0500003 | 不支持解密器 |
| encryptErrKeyAcquire | 0x0500004 | 密钥获取失败 |
| encryptErrDecryption | 0x0500005 | 解密段失败 |
| encryptErrLicenseAcquire | 0x0500006 | 许可证获取失败 |
| SRC_PLAYER_MISMATCH 错误起始值 (0x0600000 - 0x06FFFFF) | | |
| srcPlayerMismatchUnknown | 0x0600000 | 通用没有可播放源的匹配技术播放器 |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |Flash 插件未安装，如果已安装，则会播放源。 或者 Flash 30 已安装，并且正在播放 AES 内容。  如果是这种情况，请尝试使用其他浏览器。 截至 6 月 7 日，目前不支持 Flash 30。 有关更多详细信息，请参阅[已知问题](azure-media-player-known-issues.md)。 注意：如果为 0x00600003，则不会安装 Flash 和 Silverlight（如果已在 techOrder 中指定）。|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | 未安装 Silverlight 插件，如果已安装，则会播放源。 注意：如果为 0x00600003，则不会安装 Flash 和 Silverlight（如果已在 techOrder 中指定）。 |
| | 0x00600003 | 不会安装 Flash 和 Silverlight（如果已在 techOrder 中指定）。 |
| 未知错误 (0x0FF00000) | | |
| errUnknown | 0xFF00000 | 未知错误 |

## <a name="user-error-messages"></a>使用错误消息 ##

显示的用户消息基于错误代码的位 27-20。

- MEDIA_ERR_ABORTED (1) -“已中止视频播放”
- MEDIA_ERR_NETWORK (2) -“网络错误导致视频下载中途失败。”
- MEDIA_ERR_DECODE (3) - “视频播放已中止，原因是视频损坏或浏览器不支持视频使用的功能。”
- MEDIA_ERR_SRC_NOT_SUPPORTED (4) -“无法加载视频，原因是服务器或网络故障，或者格式不受支持。”
- MEDIA_ERR_ENCRYPTED (5) -“已加密视频，我们没有密钥可对其进行解密。”
- SRC_PLAYER_MISMATCH (6) -“找不到与此视频兼容的源。”
- MEDIA_ERR_UNKNOWN (0xFF) -“发生未知错误。”

## <a name="examples"></a>示例 ##

### <a name="0x10600001"></a>0x10600001 ##

“找不到与此视频兼容的源。” 向最终用户显示。

没有可以播放请求的源的技术播放器，但如果安装了 Flash 插件，则可能会播放源。  

### <a name="0x20200194"></a>0x20200194 ###

“网络错误导致视频下载中途失败。” 向最终用户显示。

AzureHtml5JS 无法从 http 404 响应中播放。

### <a name="categorizing-errors"></a>对错误进行分类 ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>捕获特定错误 ###

以下代码仅捕获 404 个错误：

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)