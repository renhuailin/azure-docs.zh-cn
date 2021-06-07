---
title: 教程-$member 匹配操作-适用于 FHIR 的 Azure API
description: 本教程介绍 $member 匹配操作，该操作定义为 Da Vinci Health Record (HRex) 的一部分。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/01/2021
ms.openlocfilehash: a6d24d0ebf91e2ae6992f6b101b8be0d4a0d4201
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111562616"
---
# <a name="member-match-operation"></a>$member 匹配操作

[$member-match](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html) 是指定义为 Da Vinci Health 记录交换 (HRex) 一部分的操作。 在本指南中，我们将逐步介绍 $member 匹配的定义以及如何使用它。

## <a name="overview-of-member-match"></a>$Member-match 概述

创建 $member 匹配操作是为了帮助提供付款人到付款人的数据交换，这允许新付款人从患者的前一付款人获取患者的唯一标识符。 $Member 匹配操作要求在请求正文中传递三个信息片段：

* 患者人口统计

* 旧覆盖范围信息

* 基于我们的实现 (不需要新的覆盖范围信息) 

传入数据后，用于 FHIR 的 Azure API 将验证它是否能够找到与传入的旧覆盖信息完全匹配的患者。 如果找到结果，则响应将是一个捆绑，其中包含原始患者数据以及从旧付款人中添加的新标识符以及旧的覆盖信息。

> [!NOTE]
> 该规范介绍传入和返回新的覆盖范围信息。 我们决定忽略这些数据以使结果更小。

## <a name="example-of-member-match"></a>$member 匹配的示例

若要使用 $member 匹配，请使用以下调用：

`POST {{fhirurl}}/Patient/$member-match`

需要在正文中包括一个参数资源，其中包括患者、旧覆盖面和新的覆盖范围。 若要查看 JSON 表示形式，请参阅 [$member-match 示例请求](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html)。

如果找到一个匹配项，则会收到一个200响应，其中添加了另一个标识符：

:::image type="content" source="media/cms-tutorials/two-hundred-response.png" alt-text="200亿响应代码。":::

如果 $member 匹配找不到唯一匹配项，则将收到422响应，其中包含错误代码。

## <a name="next-steps"></a>后续步骤

在本指南中，已了解 $member 匹配操作。 接下来，可以了解如何在 Touchstone 中测试 Da Vinci 付款人 Data Exchange IG，这需要 $member 匹配操作。

>[!div class="nextstepaction"]
>[Davinci.txt PDex](davinci-pdex-tutorial.md)