---
author: baanders
description: 描述如何验证 Azure 数字孪生模型的文件
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 32c02750f99f1aa6733d9c6dd673ffe4964978f4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303743"
---
> [!TIP]
> 创建模型后，建议先离线对其进行验证，然后再将其上传到 Azure 数字孪生实例。

有一个与语言无关的示例可用于验证模型文档，以确保 DTDL 在上传到实例之前是正确的。 该示例位于这里：[DTDL 验证程序示例](/samples/azure-samples/dtdl-validator/dtdl-validator)。

DTDL 验证程序示例基于.NET DTDL 分析程序库构建，该库可以在 NuGet 上作为客户端库使用：[Microsoft.Azure.DigitalTwins.Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)。 你也可以直接使用该库来设计自己的验证解决方案。 使用分析程序库时，请确保使用与 Azure 数字孪生正在运行的版本兼容的版本。 目前，此版本为 3.12.4。

若要详细了解该验证程序示例和分析程序库（包括用法示例），请参阅[操作指南：分析和验证模型](../articles/digital-twins/how-to-parse-models.md)。