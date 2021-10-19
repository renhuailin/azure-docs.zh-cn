---
title: 教程-$member 匹配操作
description: 引入 $member 匹配操作，该操作定义为 Da Vinci Health 记录的一部分 Exchange (HRex) 。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 08/06/2021
ms.openlocfilehash: 2771458c6c9f23c9e87691e1a74d3e29986ad482
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778258"
---
# <a name="member-match-operation-in-fhir-service"></a>FHIR 服务中的 $member 匹配操作

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[$member-match](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html)是指定义为 Da Vinci Health 记录一部分的操作 Exchange (HRex) 。 在本指南中，我们将逐步介绍 $member 匹配的定义以及如何使用它。

## <a name="overview-of-member-match"></a>$Member-match 概述

创建 $member 匹配操作是为了帮助提供付款人到付款人的数据交换，这允许新付款人从患者的前一付款人获取患者的唯一标识符。 $Member 匹配操作要求在请求正文中传递三个信息片段：

* 患者人口统计
* 旧覆盖范围信息
* 基于我们的实现 (不需要新的覆盖范围信息) 

传入数据后，Azure 医疗保健 Api 中的 FHIR 服务 (特此称为 FHIR 服务) 验证它是否能够找到与传入的旧覆盖信息完全匹配的患者。 如果找到结果，则响应将是一个捆绑，其中包含原始患者数据以及从旧付款人中添加的新标识符以及旧的覆盖信息。

> [!NOTE]
> 该规范介绍传入和返回新的覆盖范围信息。 我们决定忽略这些数据以使结果更小。

## <a name="example-of-member-match"></a>$member 匹配的示例

若要使用 $member 匹配，请使用以下调用：

`POST {{fhirurl}}/Patient/$member-match`

需要在正文中包括一个参数资源，其中包括患者、旧覆盖面和新的覆盖范围。 若要查看 JSON 表示形式，请参阅 [$member-match 示例请求](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html)。

如果找到一个匹配项，则会收到一个200响应，其中添加了另一个标识符：

:::image type="content" source="media/centers-medicare-services-tutorials/two-hundred-response.png" alt-text="200亿响应代码。":::

如果 $member 匹配找不到唯一匹配项，则将收到422响应，其中包含错误代码。

## <a name="next-steps"></a>后续步骤

在本指南中，已了解 $member 匹配操作。 接下来，可以了解如何在 Touchstone 中测试 Da Vinci 付款人数据 Exchange IG，这需要 $member 匹配操作。

>[!div class="nextstepaction"]
>[Davinci.txt PDex](davinci-pdex-tutorial.md)
