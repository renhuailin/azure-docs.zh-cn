---
title: 在门户中创建 Azure Functions 高级计划
description: 了解如何使用 Azure 门户创建在高级计划中运行的函数应用。
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 20921423247dda3cbb39b58dcc805dac6d367390
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937600"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>在 Azure 门户中创建高级计划函数应用

Azure Functions 提供了一个可缩放的高级计划，该计划提供虚拟网络连接、无冷启动和高级硬件。 若要了解详细信息，请参阅 [Azure Functions 高级计划](functions-premium-plan.md)。 

本文介绍如何使用 Azure 门户在高级计划中创建 function app。 

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-function-app"></a>创建函数应用

必须使用 Function App 托管函数的执行。 函数应用可将函数分组为逻辑单元，以便更轻松地管理、部署、缩放和共享资源。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

此时，你可以在新的 function app 中创建函数。 这些函数可以利用 [高级计划](functions-premium-plan.md)的好处。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 HTTP 触发的函数](functions-create-first-azure-function.md#create-function)
