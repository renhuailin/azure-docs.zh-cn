---
title: API 管理策略示例 - 基于 JWT 声明授予访问权限
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何基于 JWT 声明授予对 API 中特定 HTTP 方法的访问权限。
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
ms.openlocfilehash: 1df5f4b874ff3e5e05ab6b72a786364916ae0f53
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618811"
---
# <a name="authorize-access-based-on-jwt-claims"></a>基于 JWT 声明授权访问权限

本文介绍 Azure API 管理策略示例，该示例演示如何基于 JWT 声明授予对 API 中特定 HTTP 方法的访问权限。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-reference.md)