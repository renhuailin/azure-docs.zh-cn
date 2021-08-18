---
title: 为 Azure 事件网格主题或域配置专用终结点
description: 本文介绍如何为 Azure 事件网格主题或域配置专用终结点。
ms.topic: how-to
ms.date: 07/22/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e97883be779d0bea22cc2169754cac3b17d83db7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469554"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>为 Azure 事件网格主题或域配置专用终结点
使用[专用终结点](../private-link/private-endpoint-overview.md)，可以允许事件通过[专用链接](../private-link/private-link-overview.md)安全地从虚拟网络直接进入主题和域，而无需通过公共 Internet。 专用终结点为主题或域使用 VNet 地址空间中的 IP 地址。 有关详细概念信息，请参阅[网络安全](network-security.md)。

本文介绍如何为主题或域配置专用终结点。

## <a name="use-azure-portal"></a>使用 Azure 门户 
本部分演示如何使用 Azure 门户为主题或域创建专用终结点。

> [!NOTE]
> 本部分中显示的步骤主要适用于主题。 可以按照类似的步骤来为域创建专用终结点。 

1. 登录 [Azure 门户](https://portal.azure.com)并导航到主题或域。
2. 切换到主题页的“网络”选项卡。 在工具栏上选择“+ 专用终结点”。

    ![添加专用终结点](./media/configure-private-endpoints/add-button.png)
2. 在“基本信息”页上执行以下步骤： 
    1. 选择要在其中创建专用终结点的 Azure 订阅。 
    2. 为专用终结点选择“Azure 资源组”。 
    3. 为终结点输入名称。 
    4. 为终结点选择“区域”。 专用终结点必须与虚拟网络位于同一区域，但可以与专用链接资源（在本例中为事件网格主题）位于不同的区域。 
    5. 然后，选择页面底部的“下一步: 资源 >”。 

      ![专用终结点 -“基本信息”页](./media/configure-private-endpoints/basics-page.png)
3. 在“资源”页上执行以下步骤： 
    1. 对于连接方法，如果选择了“连接到我的目录中的 Azure 资源”，请执行以下步骤。 此示例演示如何连接到你的目录中的 Azure 资源。 
        1. 选择你的主题/域所在的 Azure 订阅 。 
        1. 对于“资源类型”，选择“Microsoft.EventGrid/主题”或“Microsoft.EventGrid/域”作为资源类型   。
        2. 对于“资源”，请从下拉列表中选择一个主题/域。 
        3. 请确认“目标子资源”设置为“主题”或“域”（基于所选的资源类型）  。    
        4. 在页面底部选择“下一步:配置 >”按钮。 

            ![显示“创建专用终结点 - 资源”页的屏幕截图。](./media/configure-private-endpoints/resource-page.png)
    2. 如果选择了“使用资源 ID 或别名连接到资源”，请执行以下步骤：
        1. 输入资源的 ID。 例如：`/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`。  
        2. 对于“资源”，请输入“主题”或“域”  。 
        3. （可选）添加一条请求消息。 
        4. 在页面底部选择“下一步:配置 >”按钮。 

            ![专用终结点 -“资源”页](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. 在“配置”页上，选择要在其中部署专用终结点的虚拟网络中的子网。 
    1. 选择一个虚拟网络。 下拉列表中仅列出了当前所选订阅和位置中的虚拟网络。 
    2. 在所选的虚拟网络中选择一个“子网”。 
    3. 在完成时选择“下一步:标记 >”按钮，它位于页面底部。 

    ![专用终结点 -“配置”页](./media/configure-private-endpoints/configuration-page.png)
5. 在“标记”页上，创建要与专用终结点资源关联的任何标记（名称和值）。 然后选择页面底部的“查看 + 创建”按钮。 
6. 在“查看 + 创建”页上查看所有设置，然后选择“创建”以创建专用终结点 。 

    ![专用终结点 -“查看 + 创建”页](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>管理专用链接连接

创建专用终结点时，必须批准连接。 如果要为其创建专用终结点的资源位于你的目录中，在拥有足够权限的前提下，你可以批准连接请求。 如果要连接到另一个目录中的 Azure 资源，必须等待该资源的所有者批准你的连接请求。

有四种预配状态：

| 服务操作 | 服务使用者专用终结点状态 | 说明 |
|--|--|--|
| 无 | 挂起的 | 连接是手动创建的，正等待专用链接资源所有者批准。 |
| 审批 | 已批准 | 连接已自动或手动批准，随时可供使用。 |
| 拒绝 | 已拒绝 | 连接已被专用链接资源所有者拒绝。 |
| 删除 | 已断开连接 | 连接已被专用链接资源所有者删除，专用终结点仅供参考，应将其删除以清理资源。 |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>如何管理专用终结点连接
以下部分说明了如何批准或拒绝专用终结点连接。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，键入“事件网格主题”或“事件网格域” 。
1. 选择要管理的主题或域 。
1. 选择“网络”选项卡。
1. 如果有任何挂起的连接，则会列出预配状态为“挂起”的连接。 

### <a name="to-approve-a-private-endpoint"></a>批准专用终结点
可以批准处于挂起状态的专用终结点。 若要批准，请按照下列步骤进行操作： 

> [!NOTE]
> 本部分中显示的步骤主要适用于主题。 可以使用类似的步骤来批准域的专用终结点。 

1. 选择要批准的“专用终结点”，然后在工具栏上选择“批准” 。

    ![专用终结点 - 挂起状态](./media/configure-private-endpoints/pending.png)
1. 在“批准连接”对话框上，输入注释（可选），然后选择“是” 。 

    ![专用终结点 - 批准](./media/configure-private-endpoints/approve.png)
1. 确认终结点的状态为“已批准”。 

    ![专用终结点 - 已批准状态](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>拒绝专用终结点
可以拒绝处于挂起状态或已批准状态的专用终结点。 若要拒绝，请按照下列步骤操作： 

> [!NOTE]
> 本部分中显示的步骤适用于主题。 可以使用类似的步骤来拒绝域的专用终结点。 

1. 选择要拒绝的“专用终结点”，然后在工具栏上选择“拒绝” 。

    ![显示“网络 - 专用终结点连接”的屏幕截图，其中已选中“拒绝”。](./media/configure-private-endpoints/reject-button.png)
1. 在“拒绝连接”对话框上，输入注释（可选），然后选择“是” 。 

    ![专用终结点 - 拒绝](./media/configure-private-endpoints/reject.png)
1. 确认终结点的状态为“已拒绝”。 

    ![专用终结点 - 已拒绝状态](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > 拒绝后，无法在 Azure 门户中批准该专用终结点。 


## <a name="use-azure-cli"></a>使用 Azure CLI
若要创建专用终结点，请使用 [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) 方法，如以下示例所示：

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

有关此示例中使用的参数的说明，请参阅关于 [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) 的文档。 在此示例中，需要注意以下几点： 

- 对于 `private-connection-resource-id`，请指定主题或域的资源 ID 。 前面的示例使用的类型是主题。
- 对于 `domain`，请指定 `group-ids` 或 `topic`。 在前面的示例中，使用的是 `topic`。 

若要删除专用终结点，请使用 [az network private-endpoint delete](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) 方法，如以下示例所示：

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> 本部分中显示的步骤适用于主题。 可以按照类似的步骤来为域创建专用终结点。 



### <a name="prerequisites"></a>先决条件
通过运行以下命令更新适用于 CLI 的 Azure 事件网格扩展： 

```azurecli-interactive
az extension update -n eventgrid
```

如果未安装扩展，请运行以下命令进行安装： 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>创建专用终结点
若要创建专用终结点，请使用 [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) 方法，如以下示例所示：

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

有关此示例中使用的参数的说明，请参阅关于 [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) 的文档。 在此示例中，需要注意以下几点： 

- 对于 `private-connection-resource-id`，请指定主题或域的资源 ID 。 前面的示例使用的类型是主题。
- 对于 `domain`，请指定 `group-ids` 或 `topic`。 在前面的示例中，使用的是 `topic`。 

若要删除专用终结点，请使用 [az network private-endpoint delete](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) 方法，如以下示例所示：

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> 本部分中显示的步骤适用于主题。 可以按照类似的步骤来为域创建专用终结点。 

#### <a name="sample-script"></a>示例脚本
下面是创建以下 Azure 资源的示例脚本：

- 资源组
- 虚拟网络
- 虚拟网络中的子网
- Azure 事件网格主题
- 主题的专用终结点

> [!NOTE]
> 本部分中显示的步骤适用于主题。 可以使用类似的步骤来创建域的专用终结点。

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>批准专用终结点
下面的示例 CLI 代码片段演示如何批准专用终结点连接。 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>拒绝专用终结点
下面的示例 CLI 代码片段演示如何拒绝专用终结点连接。 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>禁用公用网络访问
默认情况下，为事件网格主题或域启用公共网络访问。 若仅允许通过专用终结点进行访问，请运行以下命令来禁用公共网络访问：  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>使用 PowerShell
本部分演示如何使用 PowerShell 为主题或域创建专用终结点。 

### <a name="prerequisite"></a>先决条件
按照[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)中的说明来创建 Azure Active Directory 应用程序并记下“Directory (租户) ID”、“应用程序(客户端) ID”和“应用程序(客户端)机密”的值  。 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>准备用于 REST API 调用的令牌和标头 
运行以下先决条件命令以获取用于 REST API 调用和授权的身份验证令牌以及其他标头信息。 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>创建已禁用终结点网络策略的子网

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>创建具有专用终结点的事件网格主题

> [!NOTE]
> 本部分中显示的步骤适用于主题。 可以按照类似的步骤来为域创建专用终结点。 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

验证是否已创建终结点时，应会看到如下结果：

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>批准专用终结点连接
下面的示例 PowerShell 代码片段演示如何批准专用终结点。 

> [!NOTE]
> 本部分中显示的步骤适用于主题。 可以使用类似的步骤来批准域的专用终结点。 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>拒绝专用终结点连接
以下示例演示如何使用 PowerShell 拒绝专用终结点。 可从上一个 GET 命令的结果中获取专用终结点的 GUID。 

> [!NOTE]
> 本部分中显示的步骤适用于主题。 可以使用类似的步骤来拒绝域的专用终结点。 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

即使通过 API 拒绝连接后，仍可以批准该连接。 如果使用 Azure 门户，则无法批准已拒绝的终结点。 

## <a name="next-steps"></a>后续步骤
* 若要了解如何配置 IP 防火墙设置，请参阅[为 Azure 事件网格主题或域配置 IP 防火墙](configure-firewall.md)。
* 若要排查网络连接问题，请参阅[排查网络连接问题](troubleshoot-network-connectivity.md)
