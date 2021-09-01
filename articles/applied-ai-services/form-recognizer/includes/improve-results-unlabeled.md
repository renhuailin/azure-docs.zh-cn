---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: lajanuar
ms.openlocfilehash: c41d7195bb1c836766248d02bec25218fd5a4f92
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326837"
---
检查 `"pageResults"` 节点下每个键/值结果的 `"confidence"` 值。 还应查看 `"readResults"` 节点中的置信度分数，这些分数对应于文本读取操作。 读取结果的置信度不影响键/值提取结果的置信度，因此二者都应检查。
* 如果读取操作的置信度分数低，请尝试提高输入文档的质量（请参阅[输入要求](../overview.md#input-requirements)）。
* 如果键/值提取操作的置信度分数低，请确保所分析的文档与训练集中使用的文档是相同的类型。 如果训练集中的文档在外观上有差异，请考虑将它们拆分成不同的文件夹，针对每项差异训练一个模型。

目标置信度分数将取决于用例，但通常情况下，最好将分数设定为 80% 或更高。 对于更敏感的情况，如读取医疗记录或账单，建议将分数设置为 100%。