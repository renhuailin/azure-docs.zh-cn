---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424656"
---
## <a name="invoke-the-function-on-azure"></a>在 Azure 上调用函数

由于函数使用 HTTP 触发器，因此，可以通过在浏览器中或使用 curl 等工具，向此函数的 URL 发出 HTTP 请求来调用它。 

# <a name="browser"></a>[浏览器](#tab/browser)

将 publish 命令的输出中显示的完整“调用 URL”复制到浏览器的地址栏，并追加查询参数 `&name=Functions`。 浏览器显示的输出应与本地运行函数时显示的输出类似。

![在 Azure 上运行函数后浏览器中的输出](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

结合“调用 URL”运行 [`curl`](https://curl.haxx.se/)，并追加参数 `&name=Functions`。 该命令的输出应是文本“Hello Functions”。

![使用 curl 在 Azure 上运行函数后的输出](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
