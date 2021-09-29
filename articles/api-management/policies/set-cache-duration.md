---
title: API 管理策略示例 - 设置响应缓存持续时间
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何使用后端发送的 Cache-Control 标头中的 maxAge 值设置响应缓存持续时间。
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: danlep
ms.openlocfilehash: 512310da23b4157bfcd732fdf429bdc2243d630c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651952"
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