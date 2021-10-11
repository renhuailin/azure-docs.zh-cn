---
title: 快速入门：将自定义事件发送到 Azure 函数 - 事件网格
description: 快速入门：使用 Azure 事件网格和 Azure CLI 或门户发布一个主题，然后订阅该事件。 Azure 函数用于终结点。
ms.date: 09/28/2021
ms.topic: quickstart
ms.openlocfilehash: c6f0377b1b6489927de6a980bf4fc1ecb080e051
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277356"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>快速入门：使用事件网格将自定义事件路由到 Azure 函数

Azure 事件网格是针对云的事件处理服务。 Azure 函数是受支持的事件处理程序之一。 在本文中，将使用 Azure 门户创建一个自定义主题，然后订阅该自定义主题，再触发可查看结果的事件。 将事件发送至 Azure 函数。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>创建 Azure 函数
在订阅自定义主题之前，创建一个函数来处理事件。 

1. 按照[创建函数应用](../azure-functions/functions-get-started.md)中的说明创建函数应用。
1. 在“函数应用”页中，选择左侧菜单中的“函数” 。 
1. 选择工具栏上的“+ 创建”以创建函数。
1. 在“创建函数”页上执行以下步骤：
    1. 此步骤是可选的。 对于“开发环境”，选择要用于处理函数代码的开发环境。 
    1. 在“选择模板”部分中，选择“Azure 事件网格触发器” 。 
    1. 为函数输入名称。 本示例中为 HandleEventsFunc。 

        :::image type="content" source="./media/custom-event-to-function/function-event-grid-trigger.png" lightbox="./media/custom-event-to-function/function-event-grid-trigger.png" alt-text="选择事件网格触发器。":::
4. 使用“代码 + 测试”页可以查看函数的现有代码并对其进行更新。 

    :::image type="content" source="./media/custom-event-to-function/function-code-test-menu.png" alt-text="图像显示了已选中 Azure 函数的“代码 + 测试”菜单。":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>创建自定义主题

事件网格主题提供用户定义的终结点，可向其发布事件。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在左侧导航菜单中选择“所有服务”，搜索“事件网格”，然后选择“事件网格主题”。    

    :::image type="content" source="./media/custom-event-to-function/select-event-grid-topics.png" alt-text="图像显示了已选中“事件网格主题”。":::
3. 在“事件网格主题”页上的工具栏中选择“添加”。   

    :::image type="content" source="./media/custom-event-to-function/add-event-grid-topic-button.png" alt-text="图像显示用于创建事件网格主题的“创建”按钮。":::
4. 在“创建主题”页上执行以下步骤：
    1. 选择 **Azure 订阅**。
    2. 选择与前面的步骤一致的资源组。
    3. 为自定义主题提供唯一的 **名称**。 主题名称必须唯一，因为它由 DNS 条目表示。 请不要使用图中所示的名称。 而是创建自己的名称 - 它必须介于 3 到 50 个字符之间，并且只包含值 a-z、A-Z、0-9 和“-”。
    4. 选择事件网格主题的 **位置**。
    5. 选择“查看 + 创建”。 
    
        :::image type="content" source="./media/custom-event-to-function/create-custom-topic.png" alt-text="图像显示了“创建主题”页。":::      
    1. 在“查看 + 创建”页上查看设置，然后选择“创建” 。 
5. 创建自定义主题后，选择“转到资源”链接以查看创建的主题的以下“事件网格主题”页。 

    :::image type="content" source="./media/custom-event-to-function/event-grid-topic-home-page.png" alt-text="图像显示了事件网格自定义主题的主页。":::

## <a name="subscribe-to-custom-topic"></a>订阅自定义主题

订阅事件网格主题，以告知事件网格要跟踪哪些事件，以及要将事件发送到何处。

1. 现在，请在自定义主题的“事件网格主题”页上的工具栏中，选择“+ 事件订阅”。 

    :::image type="content" source="./media/custom-event-to-function/new-event-subscription.png" alt-text="图像显示了工具栏上选中的“添加事件订阅”。":::
2. 在“创建事件订阅”页上执行以下步骤：
    1. 输入事件订阅的“名称”  。
    3. 选择“Azure 函数”作为“终结点类型”   。 
    4. 选择“选择终结点”。  

        :::image type="content" source="./media/custom-event-to-function/provide-subscription-values.png" alt-text="图像显示了事件订阅值。":::
    5. 对于函数终结点，选择你的函数应用所在的 Azure 订阅和资源组，然后选择之前创建的函数应用和函数。 选择“确认所选内容”  。

        :::image type="content" source="./media/custom-event-to-function/provide-endpoint.png" alt-text="图像显示“选择 Azure 函数”页，其中显示已选中之前创建的函数。":::
    6. 此步骤是可选的，但建议在生产方案中使用。 在“创建事件订阅”页上，切换到“高级功能”选项卡，并设置“每批最大事件数”和“首选批大小(KB)”的值。 
    
        批处理可提高吞吐量。 对于“每批最大事件数”，请设置订阅要在一个批中包含的事件的最大数目。 首选批大小设置批大小的首选上限（以 KB 为单位），但如果单个事件大于此阈值，则可以超出此上限。
    
        :::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="图像显示了事件订阅的批处理设置。":::
    6. 在“创建事件订阅”页上，选择“创建”。 

## <a name="send-an-event-to-your-topic"></a>向主题发送事件

现在，让我们触发一个事件，看事件网格如何将消息分发到终结点。 使用 Azure CLI 或 PowerShell 向自定义主题发送测试性事件。 通常情况下，应用程序或 Azure 服务会发送事件数据。

第一个示例使用 Azure CLI。 它获取自定义主题的 URL 和密钥，以及示例事件数据。 将自定义主题名称用于 `<topic name>`。 它将创建示例事件数据。 JSON 的 `data` 元素是事件的有效负载。 可以将任何格式正确的 JSON 置于此字段中。 也可将主题字段用于高级路由和筛选。 CURL 是发送 HTTP 请求的实用工具。


### <a name="azure-cli"></a>Azure CLI
1. 在 Azure 门户中选择“Cloud Shell”。  在 Cloud Shell 窗口的左上角选择“Bash”。  

    :::image type="content" source="./media/custom-event-quickstart-portal/cloud-shell-bash.png" alt-text="图像显示了“Cloud Shell - Bash”窗口":::
1. 运行以下命令以获取主题的 **终结点**：复制并粘贴该命令后，更新 **主题名称** 和 **资源组名称**，然后运行该命令。 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. 运行以下命令以获取自定义主题的 **密钥**：复制并粘贴该命令后，更新 **主题名称** 和 **资源组名称**，然后运行该命令。 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. 复制以下包含事件定义的语句，然后按 **ENTER**。 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. 运行以下 **Curl** 命令以发布事件：

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
第二个示例使用 PowerShell，执行的步骤类似。

1. 在 Azure 门户中选择“Cloud Shell”  （或者转到 `https://shell.azure.com/`）。 在 Cloud Shell 窗口的左上角选择“PowerShell”。  参阅“Azure CLI”部分的示例 **Cloud Shell** 窗口图像。
2. 设置以下变量。 复制并粘贴每个命令后，更新 **主题名称** 和 **资源组名称**，然后运行该命令：

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. 运行以下命令以获取主题的 **终结点** 和 **密钥**：

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. 准备事件。 在 Cloud Shell 窗口中复制并运行这些语句。 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. 使用 **Invoke-WebRequest** cmdlet 发送事件。 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-that-function-received-the-event"></a>验证函数是否已收到事件
现已触发事件，并且事件网格已将消息发送到订阅时配置的终结点。 导航到事件网格触发器函数并打开日志。 这时应该会在日志中看到该事件的数据有效负载的副本。 如果不确定，请先打开日志窗口，或点击重新连接，然后再次尝试发送测试事件。

:::image type="content" source="./media/custom-event-to-function/successful-function.png" alt-text="图像显示了包含日志的 Azure 函数的“监视”视图。":::

## <a name="clean-up-resources"></a>清理资源
如果打算继续处理此事件，请不要清除本文中创建的资源。 否则，请删除本文中创建的资源。

1. 在左侧菜单中选择“资源组”。 如果左侧菜单中未显示此选项，请在左侧菜单中选择“所有服务”，然后选择“资源组”。   
2. 选择资源组以启动“资源组”页。  
3. 在工具栏中选择“删除资源组”。 
4. 输入资源组的名称以确认删除，然后选择“删除”。  

    ![资源组](./media/custom-event-to-function/delete-resource-groups.png)

    图中显示的另一个资源组是 Cloud Shell 窗口创建并使用的。 如果你以后不打算使用 Cloud Shell 窗口，请删除该资源组。 

## <a name="next-steps"></a>后续步骤

了解如何创建主题和事件订阅以后，即可进一步学习事件网格的功能：

- [关于事件网格](overview.md)
- [将 Blob 存储事件路由到自定义 Web 终结点](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [通过 Azure 事件网格和逻辑应用监视虚拟机的更改](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md)

请查看以下示例，了解如何使用不同的编程语言将事件发布到事件网格以及如何从事件网格使用事件。 

- [适用于 .NET 的 Azure 事件网格示例](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/)
- [适用于 Java 的 Azure 事件网格示例](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
- [适用于 Python 的 Azure 事件网格示例](/samples/azure/azure-sdk-for-python/eventgrid-samples/)
- [适用于 JavaScript 的 Azure 事件网格示例](/samples/azure/azure-sdk-for-js/eventgrid-javascript/)
- [适用于 TypeScript 的 Azure 事件网格示例](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
