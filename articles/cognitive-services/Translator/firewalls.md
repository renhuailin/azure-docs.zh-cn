---
title: 在防火墙后面翻译 - 翻译器
titleSuffix: Azure Cognitive Services
description: Azure 认知服务翻译器可以使用域名或 IP 筛选在防火墙后面翻译。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: f5dd72328180574809c812d670f8165ad84963ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897741"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>如何使用翻译器在 IP 防火墙后面翻译

翻译器可以使用域名或 IP 筛选在防火墙后面翻译。 域名筛选是首选方法。 我们建议不要在经过 IP 筛选的防火墙后面运行 Microsoft Translator。 安装程序在将来可能会发生中断，恕不另行通知。

## <a name="translator-ip-addresses"></a>Translator IP 地址
api.cognitive.microsofttranslator.com 的 IP 地址 - 截至 2019 年 8 月 21 日的翻译器：

* **亚太区：** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **欧洲：** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **北美：** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用翻译器在 IP 防火墙后面翻译](reference/v3-0-translate.md)
