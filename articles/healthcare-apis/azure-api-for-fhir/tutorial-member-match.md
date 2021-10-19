---
title: 教程 - $member匹配操作 - Azure API for FHIR
description: 本教程介绍在 HRex $member Da Vinci 运行状况记录中定义的Exchange (匹配) 。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/01/2021
ms.openlocfilehash: 592a5a473cf000713bc8a266e36516bc777a6e62
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781975"
---
# <a name="member-match-operation-for-azure-api-for-fhir"></a>$member的匹配Azure API for FHIR

[$member匹配](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html)是定义为 Da Vinci Health Record Exchange (HRex) 。 在本指南中，我们将演练$member匹配是什么以及如何使用它。

## <a name="overview-of-member-match"></a>$member-match 概述

创建了$member匹配操作，通过允许新支付方从患者以前的支付方获取患者的唯一标识符，帮助进行支付方到支付方的数据交换。 $member匹配操作需要在请求正文中传递三条信息：

* 患者人口统计

* 旧的覆盖率信息

* 根据我们的实现 (，不需要新的覆盖范围) 

传入数据后，Azure API for FHIR验证是否可以找到与传入的旧覆盖率信息完全匹配的患者。 如果找到结果，则响应将是一个捆绑包，包含原始患者数据，以及从旧支付者添加的新标识符以及旧的覆盖率信息。

> [!NOTE]
> 该规范描述如何传递和返回新的覆盖率信息。 我们决定省略该数据，以减小结果。

## <a name="example-of-member-match"></a>$member匹配的示例

若要$member匹配，请使用以下调用：

`POST {{fhirurl}}/Patient/$member-match`

需要在人体中包含参数资源，该资源包括患者、旧覆盖率和新覆盖率。 若要查看 JSON 表示形式，请参阅$member [匹配示例请求](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html)。

如果找到单个匹配项，将收到 200 响应，并添加另一个标识符：

:::image type="content" source="media/cms-tutorials/two-hundred-response.png" alt-text="200 百个响应代码。":::

如果$member匹配找不到唯一匹配项，将收到包含错误代码的 422 响应。

## <a name="next-steps"></a>后续步骤

在本指南中，你已了解$member匹配操作。 接下来，可以了解如何在 Touchstone 中测试 Da Vinci payer data Exchange IG，这需要$member匹配操作。

>[!div class="nextstepaction"]
>[DaVinci PDex](../fhir/davinci-pdex-tutorial.md)