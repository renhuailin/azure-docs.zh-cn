---
title: Kubernetes 上的 Azure 事件网格 - Webhook作为事件处理程序
description: 本文介绍如何在连接到 Azure Arc 的 Kubernetes 群集上创建事件网格主题，然后为该主题创建订阅。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 06/17/2021
ms.topic: quickstart
ms.openlocfilehash: d08f7678fa2a38f501c28938395bf152c6be7126
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129715831"
---
# <a name="route-cloud-events-to-webhooks-with-azure-event-grid-on-kubernetes"></a>使用 Kubernetes 上的 Azure 事件网格将云事件路由到 Webhook
在此快速入门中，你将在 Kubernetes 上的事件网格中创建一个主题，为该主题创建订阅，然后将示例事件发送到该主题以测试方案。 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]


## <a name="prerequisites"></a>先决条件

1. [将 Kubernetes 群集连接到 Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md)。
1. [在 Kubernetes 群集上安装事件网格扩展](install-k8s-extension.md)。 此扩展将事件网格部署到 Kubernetes 群集。 


## <a name="create-a-custom-location"></a>创建自定义位置
作为 Azure 位置扩展，自定义位置允许将已启用 Azure Arc 的 Kubernetes 群集用作部署事件网格主题等资源的目标位置。 自定义位置表示群集中的命名空间，是主题和事件订阅的部署位置。 在本节中，你将创建一个自定义位置。 

1. 声明以下变量，用于保存 Azure Arc 群集名称、资源组名称和自定义位置名称的值。 将这些语句复制到编辑器，替换这些值，然后将其复制/粘贴到 Bash 窗口。  

    ```azurecli-interactive
    resourcegroupname="<AZURE RESOURCE GROUP NAME>"
    arcclustername="<AZURE ARC CLUSTER NAME>"
    customlocationname="<CUSTOM LOCATION NAME>"
    ```
1. 获取 Azure Arc 连接群集的资源 ID。 运行该命令之前，请更新 Azure Arc 群集名称和资源组参数的值。 

    ```azurecli-interactive
    hostresourceid=$(az connectedk8s show -n $arcclustername -g $resourcegroupname --query id -o tsv)    
    ```
1. 获取事件网格扩展的资源 ID。 此步骤假定为事件网格扩展提供的名称为 **eventgrid-ext**。运行该命令之前，请更新 Azure Arc 群集和资源组名称。 

    ```azurecli-interactive
    clusterextensionid=$(az k8s-extension show --name eventgrid-ext --cluster-type connectedClusters -c $arcclustername -g $resourcegroupname  --query id -o tsv)    
    ```
1. 使用上述两个值创建自定义位置。 运行该命令之前，请更新自定义位置和资源组名称。 

    ```azurecli-interactive
    az customlocation create -n $customlocationname -g $resourcegroupname --namespace arc --host-resource-id $hostresourceid --cluster-extension-ids $clusterextensionid    
    ```
1. 获取自定义位置的资源 ID。 运行该命令之前，请更新自定义位置名称。 

    ```azurecli-interactive
    customlocationid=$(az customlocation show -n $customlocationname -g $resourcegroupname --query id -o tsv)    
    ```

    若要详细了解如何创建自定义位置，请参阅[在已启用 Azure Arc 的 Kubernetes 上创建和管理自定义位置](../../azure-arc/kubernetes/custom-locations.md)。 

## <a name="create-a-topic"></a>创建主题
在本节中，你将在上一步创建的自定义位置中创建一个主题。 运行该命令之前，请更新资源组和事件网格主题的名称。 如果你使用的是美国东部以外的位置，则请更新位置。 

1. 声明一个变量，用于保存主题名称。 

    ```azurecli-interactive
    topicname="<TOPIC NAME>"
    ```
4. 运行以下命令来创建一个主题。 

    ```azurecli-interactive
    az eventgrid topic create -g $resourcegroupname --name $topicname --kind azurearc --extended-location-name $customlocationid --extended-location-type customlocation --input-schema CloudEventSchemaV1_0 --location $region    
    ```

    有关 CLI 命令的详细信息，请参阅 [`az eventgrid topic create`](/cli/azure/eventgrid/topic#az_eventgrid_topic_create)。

## <a name="create-a-message-endpoint"></a>创建消息终结点

在为自定义主题创建订阅之前，请先创建事件消息的终结点。 通常情况下，终结点基于事件数据执行操作。 为了简化此快速入门，将部署用于显示事件消息的[预建的 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)。 所部署的解决方案包括应用服务计划、应用服务 Web 应用和 GitHub 中的源代码。

1. 在项目页中，选择“部署到 Azure”以将解决方案部署到订阅。 在 Azure 门户中，为参数提供值。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>
1. 部署可能需要几分钟才能完成。 部署成功后，请查看 Web 应用以确保它正在运行。 在 Web 浏览器中导航到 `https://<your-site-name>.azurewebsites.net`

    如果部署失败，请查看错误消息。 这是因为网站名称已被占用。 再次部署模板，然后为网站选择其他名称。 
1. 查看站点，但是尚未有事件发布到它。

   ![查看新站点](../media/custom-event-quickstart-portal/view-site.png)

## <a name="create-a-subscription"></a>创建订阅
订阅服务器可以注册发布到主题的事件。 若要接收任何事件，需要为感兴趣的主题创建事件网格订阅。 事件订阅定义将这些事件发送到的目标。 若要了解支持的所有目标或处理程序，请参阅[事件处理程序](event-handlers.md)。

若要使用 WebHook（HTTPS 终结点）目标创建事件订阅，请输入事件订阅名称，更新网站名称，并运行以下命令。

```azurecli-interactive
topicid=$(az eventgrid topic show --name $topicname --resource-group $resourcegroupname --query id -o tsv)
az eventgrid event-subscription create --name <EVENT SUBSCRIPTION NAME> --source-resource-id $topicid --endpoint https://<SITE NAME>.azurewebsites.net/api/updates
```


有关 CLI 命令的详细信息，请参阅 [`az eventgrid event-subscription create`](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create)。

## <a name="send-events-to-the-topic"></a>将事件发送到主题
1. 运行以下命令以获取主题的 **终结点**：复制并粘贴该命令后，更新 **主题名称** 和 **资源组名称**，然后运行该命令。 你会将示例事件发布到此主题终结点。 

    ```azurecli
    az eventgrid topic show --name $topicname -g $resourcegroupname --query "endpoint" --output tsv
    ```
2. 运行以下命令以获取自定义主题的 **密钥**：复制并粘贴该命令后，更新 **主题名称** 和 **资源组名称**，然后运行该命令。 它是主题的主密钥。 要从 Azure 门户获取此密钥，请切换到“事件网格主题”页的“访问密钥”选项卡 。 要将事件发布到自定义主题，需要访问密钥。 

    ```azurecli
    az eventgrid topic key list --name $topicname -g $resourcegroupname --query "key1" --output tsv
    ```
1. 运行以下 Curl 命令以发布事件。 在运行命令之前，请从步骤 1 和 2 中指定终结点 URL 和密钥。 

    ```bash
    curl  -k -X POST -H "Content-Type: application/cloudevents-batch+json" -H "aeg-sas-key: <KEY_FROM_STEP_2>" -g <ENDPOINT_URL_FROM_STEP_1> \
    -d  '[{ 
          "specversion": "1.0",
          "type" : "orderCreated",
          "source": "myCompanyName/us/webCommerceChannel/myOnlineCommerceSiteBrandName",
          "id" : "eventId-n",
          "time" : "2020-12-25T20:54:07+00:00",
          "subject" : "account/acct-123224/order/o-123456",
          "dataSchema" : "1.0",
          "data" : {
             "orderId" : "123",
             "orderType" : "PO",
             "reference" : "https://www.myCompanyName.com/orders/123"
          }
    }]'
    ```
    
    如果步骤 1 中的主题终结点 URL 是专用 IP 地址，例如当事件网格中转站的服务类型为 ClusterIP 时，可以从群集中的另一个 Pod 中执行“Curl” 以访问该 IP 地址。 例如，可以执行以下步骤：

    1. 创建具有以下配置的清单文件。 可能需要根据需求调整 ``dnsPolicy``。 有关详细信息，请参阅 [服务和 Pod 的 DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)。
    
        ```yml
        apiVersion: v1
        kind: Pod
        metadata:
            name: test-pod2
        spec:
            containers:
              - name: nginx
                image: nginx
            hostNetwork: true
            dnsPolicy: ClusterFirstWithHostNet       
        ```
    1. 删除 Pod。
        ```bash
            kubectl apply -f <name_of_your_yaml_manifest_file>
        ```
    1. 验证 Pod 是否正在运行。
        ```bash
            kubectl get pod test-pod
        ```
    1. 从容器启动 shell 会话
        ```bash
            kubectl exec --stdin --tty test-pod -- /bin/bash
        ```

    此时，群集中正在运行的容器中有一个 shell 会话，可以从中执行上述步骤中所述的“Curl”命令。

    > [!NOTE]
    > 若要了解如何使用编程语言发送云事件，请参阅以下示例： 
    > - [C#](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/) 中的检测示例。
    > - [Java](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
    > - [JavaScript](/samples/azure/azure-sdk-for-js/eventgrid-javascript/) 和 [TypeScript](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
    > - [Python](/samples/azure/azure-sdk-for-python/eventgrid-samples/)

### <a name="verify-in-the-event-grid-viewer"></a>在事件网格查看器中验证
现已触发事件，并且事件网格已将消息发送到订阅时配置的终结点。 查看 Web 应用以查看刚刚发送的事件。

:::image type="content" source="./media/create-topic-subscription/viewer-received-event.png" alt-text="在“事件网格查看器”中查看收到的事件":::

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 
- [事件处理程序和目标](event-handlers.md) - 提供有关 Kubernetes 上的事件网格支持的所有事件处理程序和目标的信息。 
- [事件筛选](filter-events.md) - 提供有关在事件订阅上筛选事件的信息。 
