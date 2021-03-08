---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701378"
---
1. 若要运行函数，请在 Visual Studio 中按 <kbd>F5</kbd>。 你可能需要启用防火墙例外，这样工具才能处理 HTTP 请求。 在本地运行函数时，永远不会强制实施授权级别。

2. 从 Azure Functions 运行时输出复制函数的 URL。

    ![Azure 本地运行时](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. 将 HTTP 请求的 URL 粘贴到浏览器的地址栏。 将查询字符串 `?name=<YOUR_NAME>` 追加到此 URL 并运行请求。 下图显示了浏览器中由函数返回的本地 GET 请求的响应： 

    ![浏览器中的函数 localhost 响应](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. 若要停止调试，请在 Visual Studio 中按 <kbd>Shift</kbd>+<kbd>F5</kbd>。
