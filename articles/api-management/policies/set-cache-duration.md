---
title: API 管理策略示例 - 设置响应缓存持续时间
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何使用后端发送的 Cache-Control 标头中的 maxAge 值设置响应缓存持续时间。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 088dfbcbfe00357c47de4662ac31b52e83d1d8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070973"
---
# <a name="set-response-cache-duration"></a>设置响应缓存持续时间

本文介绍 Azure API 管理策略示例，该示例演示如何使用后端发送的 Cache-Control 标头中的 maxAge 值设置响应缓存持续时间。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-reference.md)