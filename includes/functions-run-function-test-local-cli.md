---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 11d426016cfe1a8a9ff843da518f57c08881be5d
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842364"
---
## <a name="run-the-function-locally"></a>在本地运行函数

1. 通过从 LocalFunctionProj  文件夹启动本地 Azure Functions 运行时主机来运行函数：

    ```
    func start
    ```

    在输出的末尾，应显示以下行： 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > 如果 HttpExample 未按如上所示出现，则可能是在项目的根文件夹外启动了主机。 在这种情况下，请按 **Ctrl**+**C** 停止主机，导航到项目的根文件夹，然后重新运行上一命令。

1. 将此输出中 `HttpExample` 函数的 URL 复制到浏览器，并追加查询字符串 `?name=<YOUR_NAME>`，使完整 URL 类似于 `http://localhost:7071/api/HttpExample?name=Functions`。 浏览器应会显示类似于 `Hello Functions` 的消息：

    ![在浏览器中本地运行函数后的结果](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. 当你发出请求时，启动项目时所在的终端还会显示日志输出。

1. 完成后，请按 Ctrl+C 并选择 `y` 以停止函数主机 。
