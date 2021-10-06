---
title: 语言支持 - 必应视觉搜索 API
titleSuffix: Azure Cognitive Services
description: 必应视觉搜索 API 支持的自然语言、国家/地区和区域列表。 必应视觉搜索 API 支持超过 36 个国家/地区，其中很多国家/地区具有多种语言。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.openlocfilehash: a1362f9cf3963416853cf5933ee622d204578d33
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669524"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>必应视觉搜索 API 的语言和区域支持

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

必应视觉搜索 API 支持超过 36 个国家/地区，其中很多具有多种语言。 每个请求都应包含用户所选的国家/地区和语言。 知道用户的市场有助于必应返回合适的结果。 如果用户未指定国家/地区和语言，必应将尽量确定这些内容。 因为结果可能包含指向必应的链接，如果用户单击必应链接，知道国家/地区和语言可以提供首选的本地化必应用户体验。

要指定国家/地区和语言，请将 `mkt`（市场）查询参数设置为下面的“市场”表中的一个代码。 市场同时指定国家/地区和语言。 如果用户希望以其他语言查看显示文本，请将 `setLang` 查询参数设置为相应的语言代码。

另外，也可以使用 `cc` 查询参数指定国家/地区。 如果指定了国家/地区，还必须使用 `Accept-Language` HTTP 标头指定一个或多个语言代码。 支持的语言因国家/地区而异；“市场”表中提供了每个国家/地区适用的语言。



> [!NOTE]
> 存在以下市场限制：
>
> - 图像识别注释仅在英语中可用。
> - 美食、购物及包括这些页面的见解仅在美国市场中可用。


## <a name="countriesregions"></a>国家/地区

|国家/地区|代码|
|-------|----|
|阿根廷|AR|
|澳大利亚|AU|
|奥地利|AT|
|比利时|BE|
|巴西|BR|
|Canada|CA|
|智利|CL|
|丹麦|DK|
|芬兰|FI|
|法国|FR|
|德国|DE|
|香港特别行政区|HK|
|印度|IN|
|印度尼西亚|ID|
|意大利|IT|
|日本|JP|
|韩国|KR|
|马来西亚|MY|
|墨西哥|MX|
|荷兰|NL|
|新西兰|NZ|
|挪威|是|
|中国|CN|
|波兰|PL|
|葡萄牙|PT|
|菲律宾|PH|
|俄罗斯|RU|
|沙特阿拉伯|SA|
|南非|ZA|
|西班牙|ES|
|瑞典|SE|
|瑞士|CH|
|中国台湾|TW|
|土耳其|TR|
|United Kingdom|GB|
|United States|美国|


## <a name="markets"></a>市场

|国家/地区|语言|市场代码|
|-------|--------|-----------|
|阿根廷|西班牙语|es-AR|
|澳大利亚|英语|en-AU|
|奥地利|德语|de-AT|
|比利时|荷兰语|nl-BE|
|比利时|法语|fr-BE|
|巴西|葡萄牙语|pt-BR|
|加拿大|英语|en-CA|
|加拿大|法语|fr-CA|
|智利|西班牙语|es-CL|
|丹麦|丹麦语|da-DK|
|芬兰|芬兰语|fi-FI|
|法国|法语|fr-FR|
|德国|德语|de-DE|
|香港特别行政区|繁体中文|zh-HK|
|印度|英语|en-IN|
|印度尼西亚|英语|en-ID|
|意大利|意大利语|it-IT|
|日本|日语|ja-JP|
|韩国|韩语|ko-KR|
|马来西亚|英语|en-MY|
|墨西哥|西班牙语|es-MX|
|荷兰|荷兰语|nl-NL|
|新西兰|英语|en-NZ|
|中国|中文|zh-CN|
|波兰|波兰语|pl-PL|
|葡萄牙|葡萄牙语|pt-PT|
|菲律宾|英语|en-PH|
|俄罗斯|俄语|ru-RU|
|沙特阿拉伯|阿拉伯语|ar-SA|
|南非|英语|en-ZA|
|西班牙|西班牙语|es-ES|
|瑞典|瑞典语|sv-SE|
|瑞士|法语|fr-CH|
|瑞士|德语|de-CH|
|台湾|繁体中文|zh-TW|
|土耳其|土耳其语|tr-TR|
|英国|英语|en-GB|
|美国|英语|zh-CN|
|美国|西班牙语|es-US|