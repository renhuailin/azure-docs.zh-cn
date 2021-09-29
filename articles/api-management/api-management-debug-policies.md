---
title: 在 Visual Studio Code 中调试 Azure API 管理策略 | Microsoft Docs
description: 了解如何使用 Azure API 管理 Visual Studio Code 扩展调试 Azure API 管理策略
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: danlep
ms.openlocfilehash: 884ddb46c2182ae22df555b5ac32c4b4ff72c424
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625822"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>在 Visual Studio Code 中调试 Azure API 管理策略

Azure API 管理中的[策略](api-management-policies.md)提供强大的功能，可帮助 API 发布者解决身份验证、授权、限制、缓存和转换等跨领域问题。 策略是一组语句，在请求或 API 的响应时按顺序执行。 

本文介绍如何使用[适用于 Visual Studio Code 的 Azure API 管理扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)调试 API 管理策略。 

## <a name="prerequisites"></a>先决条件

* 首先请按照此[快速入门](get-started-create-service-instance.md)创建 API 管理开发人员层实例。

* 安装 [Visual Studio Code](https://code.visualstudio.com/) 和最新版本的[适用于 Visual Studio Code 的 Azure API 管理扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)。 

* 将 API 导入到 API 管理实例。 有关示例步骤，请参阅[教程：使用 Visual Studio Code 的 API 管理扩展来导入和管理 API](visual-studio-code-tutorial.md)。

## <a name="restrictions-and-limitations"></a>限制和局限

此功能仅在 API 管理的“开发人员”层中可用。 每个 API 管理实例仅支持一个并发调试会话。

## <a name="initiate-a-debugging-session"></a>启动调试会话

1. 启动 Visual Studio Code
2. 导航到 Azure 扩展下的 API 管理扩展
3. 查找要调试的 API 管理实例
4. 查找要调试的 API 和操作
5. 右键单击操作并选择“启动策略调试”

此时，扩展将尝试与 API 管理网关启动并建立调试会话。

![启动调试](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>发送测试请求
建立调试会话后，扩展将打开一个新的编辑器，让我们可以利用 [REST Client 扩展](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)创建并向该操作发送测试 HTTP 请求。

你会注意到，“Ocp-Apim-Debug”标头已经添加到请求中。 此标头是必需的，并且此值必须设置为服务级别、完全访问订阅密钥，才能触发 API 管理网关中的调试功能。

根据测试场景，在编辑器中修改 HTTP 请求。 然后单击“发送请求”，将测试请求发送到 API 管理网关。

![发送测试请求](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>调试策略
发送测试 HTTP 请求后，扩展将打开显示该操作的有效策略的调试窗口，并在第一个有效策略处停止。 

![调试策略](media/api-management-debug-policies/main-window.png)

若要遵循策略管道，可以单步执行各个策略，也可以在一个策略上设置一个断点并直接单步执行该策略。 

在“变量”面板中，可以检查系统创建的和用户创建的变量的值。 在“断点”面板中，可以看到已设置的所有断点的列表。 在“调用堆栈”面板中，可以看到当前有效的策略范围。 

如果在策略执行期间发生错误，你将在发生错误的策略处看到错误的详细信息。 

![exceptions](media/api-management-debug-policies/exception.png)

> [!TIP]
> 请记住，完成后单击“停止”按钮退出调试会话。


## <a name="next-steps"></a>后续步骤

+ 详细了解[适用于 Visual Studio Code 的 API 管理扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)。 
+ 在 [GitHub 存储库](https://github.com/Microsoft/vscode-apimanagement)中报告问题

