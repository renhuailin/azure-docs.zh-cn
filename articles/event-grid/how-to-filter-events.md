---
title: 如何筛选 Azure 事件网格的事件
description: 本文介绍在创建事件网格订阅时如何筛选事件（按事件类型、按主题、按运算符和数据等）。
ms.topic: conceptual
ms.date: 08/11/2021
ms.openlocfilehash: 666690fc7c0d21c7ae257a20b49949961d89cb60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744620"
---
# <a name="filter-events-for-event-grid"></a>筛选事件网格的事件

本文介绍如何在创建事件网格订阅时筛选事件。 若要了解事件筛选的选项，请参阅[了解事件网格订阅的事件筛选](event-filtering.md)。

## <a name="filter-by-event-type"></a>按事件类型筛选

创建事件网格订阅时，可以指定要发送到终结点的[事件类型](event-schema.md)。 本部分中的示例创建资源组的事件订阅，但会限制发送到 `Microsoft.Resources.ResourceWriteFailure` 和 `Microsoft.Resources.ResourceWriteSuccess` 的事件。 如果在按事件类型筛选事件时需要更多灵活性，请参阅按高级运算符和数据字段筛选。

### <a name="azure-powershell"></a>Azure PowerShell
对于 PowerShell，请在创建订阅时使用 `-IncludedEventType` 参数。

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

### <a name="azure-cli"></a>Azure CLI
对于 Azure CLI，请使用 `--included-event-types` 参数。 以下示例在 Bash shell 中使用 Azure CLI：

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

### <a name="azure-portal"></a>Azure 门户

1. 在“事件订阅”页上，切换到“筛选器”选项卡。  
1. 选择“事件类型筛选器”旁边的“添加事件类型”。 。 

    :::image type="content" source="./media/how-to-filter-events/add-event-type-button.png" alt-text="选择了“添加事件类型”按钮的“事件订阅”页的屏幕截图。":::    
1. 键入事件类型，然后按 ENTER。 在以下示例中，事件类型为 `Microsoft.Resources.ResourceWriteSuccess`。 

    :::image type="content" source="./media/how-to-filter-events/sample-event-type.png" alt-text="包含示例事件类型的“事件订阅”页的屏幕截图。":::    
      
### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板
对于资源管理器模板，请使用 `includedEventTypes` 属性。

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

> [!NOTE]
> 若要详细了解这些筛选器（事件类型、主题和高级），请参阅[了解事件网格订阅的事件筛选](event-filtering.md)。 

## <a name="filter-by-subject"></a>按主题筛选

可以按事件数据中的主题筛选事件。 可以指定一个值来匹配主题的开头或结尾。 如果在按主题筛选事件时需要更多灵活性，请参阅按高级运算符和数据字段筛选。

在以下 PowerShell 示例中，会创建一个按主题开头筛选的事件订阅。 使用 `-SubjectBeginsWith` 参数将事件限制为特定资源的事件。 然后传递网络安全组的资源 ID。

### <a name="azure-powershell"></a>Azure PowerShell
```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

下一个 PowerShell 示例创建 blob 存储的订阅。 其中将事件限制为主题以 `.jpg` 结尾的事件。

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

### <a name="azure-cli"></a>Azure CLI
在以下 Azure CLI 示例中，会创建一个按主题开头筛选的事件订阅。 使用 `--subject-begins-with` 参数将事件限制为特定资源的事件。 然后传递网络安全组的资源 ID。

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

下一个 Azure CLI 示例创建 blob 存储的订阅。 其中将事件限制为主题以 `.jpg` 结尾的事件。

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

### <a name="azure-portal"></a>Azure 门户

1. 在“事件订阅”页上选择“启用主题筛选”。  
1. 为以下一个或多个字段输入值：“主题开头为”和“主题结尾为”。  在以下选项中，选择两个选项。 

    :::image type="content" source="./media/how-to-filter-events/subject-filter-example.png" alt-text="包含主题筛选示例的“事件订阅”页的屏幕截图。":::
1. 如果希望事件的主题与指定的筛选器的大小写匹配，选择“区分大小写的主题匹配”选项。 

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板
在以下资源管理器模板示例中，会创建一个按主题开头筛选的事件订阅。 使用 `subjectBeginsWith` 属性将事件限制为特定资源的事件。 然后传递网络安全组的资源 ID。

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

下一个资源管理模板示例创建 blob 存储的订阅。 其中将事件限制为主题以 `.jpg` 结尾的事件。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

> [!NOTE]
> 若要详细了解这些筛选器（事件类型、主题和高级），请参阅[了解事件网格订阅的事件筛选](event-filtering.md)。 

## <a name="filter-by-operators-and-data"></a>按运算符和数据进行筛选

为提高筛选灵活性，可以使用运算符和数据属性来筛选事件。

### <a name="subscribe-with-advanced-filters"></a>订阅高级筛选器

若要了解可用于高级筛选的运算符和密钥，请参阅[高级筛选](event-filtering.md#advanced-filtering)。

这些示例创建自定义主题。 它们订阅自定义主题，并按数据对象中的值进行筛选。 将颜色属性设置为“蓝色”、“红色”或“绿色”的事件会发送到订阅。

### <a name="azure-powershell"></a>Azure PowerShell

对于 PowerShell，请使用：

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="azure-cli"></a>Azure CLI

对于 Azure CLI，请使用：

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

请注意为订阅设置[到期日期](concepts.md#event-subscription-expiration)。


### <a name="azure-portal"></a>Azure 门户

1. 在“事件订阅”页上，选择“高级筛选器”部分中的“添加新筛选器”。   

    :::image type="content" source="./media/how-to-filter-events/add-new-filter-button.png" alt-text="显示突出显示了“添加新筛选器”链接的“事件订阅”页的屏幕截图。":::    
2. 指定要比较的密钥、运算符和值。 在以下示例中，“data.color”用作密钥，“字符串在”用作运算符，而“蓝色”、“红色”和“绿色”值为指定的值。     

    :::image type="content" source="./media/how-to-filter-events/advanced-filter-example.png" alt-text="显示高级筛选器示例的屏幕截图。"::: 

    > [!NOTE]
    > 若要详细了解高级筛选器，请参阅[了解事件网格订阅的事件筛选](event-filtering.md)。 


### <a name="test-the-filter"></a>测试筛选器
若要测试筛选器，请发送将颜色字段设置为“绿色”的事件。 由于绿色是筛选器中的值之一，因此该事件将传递到终结点。

### <a name="azure-powershell"></a>Azure PowerShell
对于 PowerShell，请使用：

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

若要测试未发送事件的情形，请发送将颜色字段设置为“黄色”的事件。 “黄色”不是订阅中所指定的其中一个值，因此不会将事件发送到订阅。

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```


### <a name="azure-cli"></a>Azure CLI
对于 Azure CLI，请使用：

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

若要测试未发送事件的情形，请发送将颜色字段设置为“黄色”的事件。 “黄色”不是订阅中所指定的其中一个值，因此不会将事件发送到订阅。

对于 Azure CLI，请使用：

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

## <a name="next-steps"></a>后续步骤
若要详细了解筛选器（事件类型、主题和高级），请参阅[了解事件网格订阅的事件筛选](event-filtering.md)。 

