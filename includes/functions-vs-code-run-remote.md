---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 4b15fec0f22db740bbd7c24fcc0acf2ad1a2d1cd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493434"
---
## <a name="run-the-function-in-azure"></a>在 Azure 中运行函数

1. 返回到“Azure：函数”区域（位于边栏中），然后展开“本地项目” > “Functions” 。 右键单击 (Windows) 或按 <kbd>Ctrl -</kbd> 单击 (macOS) `HttpExample` 函数，然后选择“立即执行函数...”。

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="立即在 Azure 的 Visual Studio Code 中执行函数":::

1. 在“输入请求正文”中，你将看到请求消息正文值 `{ "name": "Azure" }`。 按 Enter 将此请求消息发送给函数。  

1. 当函数在 Azure 中执行并返回响应时，Visual Studio Code 中将引发通知。
