---
title: 获取连接字符串 - Azure 事件中心 | Microsoft Docs
description: 本文说明如何获取客户端可用于连接 Azure 事件中心的连接字符串。
ms.topic: article
ms.date: 07/23/2021
ms.openlocfilehash: 67a20adb89ffe67546e9704896542f308a243dc3
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114665374"
---
# <a name="get-an-event-hubs-connection-string"></a>获取事件中心连接字符串

若要使用事件中心，需要创建一个事件中心命名空间。 命名空间是多个事件中心或 Kafka 主题的范围容器。 此命名空间提供唯一的 [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)。 创建命名空间后，可以获取与事件中心通信所需的连接字符串。

Azure 事件中心的连接字符串中嵌入了以下组成部分：

* FQDN = 创建的事件中心命名空间的 FQDN（包括事件中心命名空间的名称，后接 servicebus.windows.net）
* SharedAccessKeyName = 为应用程序的 SAS 密钥选择的名称
* SharedAccessKey = 生成的密钥值。

连接字符串模板如下所示
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

`Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=` 是连接字符串的示例

本文逐步讲解获取连接字符串的各种方法。

## <a name="get-connection-string-from-the-portal"></a>从门户中获取连接字符串
1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 在左侧导航菜单中，选择“所有服务”。 
3. 选择“分析”部分中的“事件中心” 。 
4. 在事件中心列表中，选择事件中心。
6. 在“事件中心命名空间”页中的左侧菜单上选择“共享访问策略” 。
7. 在策略列表中选择“共享访问策略”。 默认值命名为：RootManageSharedAccessPolicy。 可以添加具有适当权限（读取、写入）的策略，并使用该策略。 

    :::image type="content" source="./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png" alt-text="事件中心共享访问策略":::
8. 选择“连接字符串 - 主密钥”字段旁边的“复制”按钮 。 

    :::image type="content" source="./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png" alt-text="事件中心 - 获取连接字符串":::

## <a name="getting-the-connection-string-with-azure-powershell"></a>使用 Azure PowerShell 获取连接字符串

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以使用 [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) 获取特定策略/规则名称的连接字符串，如下所示：

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName MYRESOURCEGROUP -NamespaceName MYEHUBNAMESPACE -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>使用 Azure CLI 获取连接字符串
可使用以下命令获取命名空间的连接字符串：

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group MYRESOURCEGROUP --namespace-name MYEHUBNAMESPACE --name RootManageSharedAccessKey
```

或者可以使用以下命令获取 EventHub 实体的连接字符串：

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group MYRESOURCEGROUP --namespace-name MYEHUBNAMESPACE --eventhub-name MYEHUB --name RootManageSharedAccessKey
```

有关事件中心 Azure CLI 命令的详细信息，请参阅[事件中心的 Azure CLI](/cli/azure/eventhubs)。

## <a name="next-steps"></a>后续步骤

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](./event-hubs-about.md)
* [创建事件中心](event-hubs-create.md)
