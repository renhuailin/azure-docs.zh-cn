---
author: baanders
description: 介绍如何验证 Azure 数字孪生模型的文件
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 58ae9c187f50bb7eb3f1ccc2a618275885121267
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563700"
---
> [!TIP]
> 创建模型后，建议在将模型上传到 Azure 数字孪生实例之前，先对其进行验证。

有一种与语言无关的示例可用于验证模型文档，以确保 DTDL 在上载到实例之前是正确的。 其位置为： [**DTDL 验证器示例**](/samples/azure-samples/dtdl-validator/dtdl-validator)。

* DTDL 验证器示例建立在一个 .NET DTDL 分析器库上，该库在 NuGet 上作为客户端库提供： [**DigitalTwins**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)。 你还可以直接使用库来设计自己的验证解决方案。 使用分析器库时，请确保使用与 Azure 数字孪生运行的版本兼容的版本。 目前，这是版本 *3.12.4*。

可以在 [*操作方法：分析和验证模型*](../articles/digital-twins/how-to-parse-models.md)中了解有关验证程序示例和分析器库的详细信息，包括用法示例。