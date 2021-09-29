---
title: 快速入门 - 使用 Azure CLI 创建服务总线队列 | Microsoft Docs
description: 在本快速入门中，你将了解如何使用 Azure CLI 创建服务总线命名空间，然后在该命名空间中创建队列。
author: spelluru
ms.topic: quickstart
ms.date: 09/28/2021
ms.author: spelluru
ms.openlocfilehash: 381d4d32d87391c0914d55690a0f4119b95a3488
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211822"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>使用 Azure CLI 创建服务总线命名空间和队列
本快速入门介绍如何使用 Azure CLI 创建服务总线命名空间和队列。 本快速入门还介绍了如何获取客户端应用程序向队列发送消息或从队列接收消息所使用的授权凭据。 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>先决条件
如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][free account]。

在本快速入门中，你将使用 Azure Cloud Shell，在登录 Azure 门户后即可启动该服务。 有关 Azure Cloud Shell 的详细信息，请参阅 [Azure Cloud Shell 概述](../cloud-shell/overview.md)。 你还可以在计算机上[安装](/cli/azure/install-azure-cli)和使用 Azure PowerShell。 

## <a name="provision-resources"></a>预配资源
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 通过选择下图中显示的图标启动 Azure Cloud Shell。 如果 Cloud Shell 处于 PowerShell 模式，请切换到 Bash 模式 。 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="启动 Cloud Shell":::
3. 运行以下命令来创建 Azure 资源组。 如有需要，请更新资源组名称和位置。 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. 运行以下命令以创建服务总线消息命名空间。

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. 运行以下命令，在上一步中创建的命名空间中创建一个队列。 在此示例中，`ContosoRG` 是在上一步中创建的资源组。 `ContosoSBusNS` 是在该资源组中创建的服务总线命名空间的名称。 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. 运行以下命令，获取命名空间的主连接字符串。 使用此连接字符串连接到队列并发送和接收消息。 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    请记下该连接字符串和队列名称。 使用它们发送和接收消息。 


## <a name="next-steps"></a>后续步骤
在本文中，你创建了服务总线命名空间并在其中创建了队列。 若要了解如何向队列发送消息或从队列接收消息，请参阅“发送和接收消息”部分中的以下某个快速入门。 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

