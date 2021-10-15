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
ms.openlocfilehash: afb0e616f01342c94734155e96367f0b453e313a
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401857"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>如何使用翻译器在 IP 防火墙后面翻译

翻译器可以使用域名或 IP 筛选在防火墙后面翻译。 域名筛选是首选方法。 如果仍然需要 IP 筛选，建议[使用服务标记获取 IP 地址详细信息](../../virtual-network/service-tags-overview.md#service-tags-on-premises)。 翻译器位于“CognitiveServicesManagement”服务标记下。 

建议不要在经过特定 IP 筛选的防火墙后面运行 Microsoft 翻译工具。 安装程序在将来可能会发生中断，恕不另行通知。

截至 2021 年 9 月 21 日，翻译器地理位置终结点的 IP 地址为：

|地理位置|基 URL（地理终结点）|IP 地址|
|:--|:--|:--|
|美国|api-nam.cognitive.microsofttranslator.com|20.42.6.144、20.49.96.128、40.80.190.224、40.64.128.192|
|欧洲|api-eur.cognitive.microsofttranslator.com|20.50.1.16、20.38.87.129|
|亚太区|api-apc.cognitive.microsofttranslator.com|40.80.170.160、20.43.132.96、20.37.196.160、20.43.66.16|
