---
title: 对 Azure 事件网格自定义主题和域启用托管标识
description: 本文介绍如何为 Azure 事件网格自定义主题或域启用托管服务标识。
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: b93fd44282d9e19e7111dd52c73d8d4c01c67a10
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278212"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>向事件网格自定义主题或域分配系统托管标识 
本文介绍如何为事件网格自定义主题或域启用系统托管标识。 若要了解托管标识，请参阅[什么是 Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="enable-identity-at-the-time-of-creation"></a>在创建时启用标识

### <a name="using-azure-portal"></a>使用 Azure 门户
在 Azure 门户中创建自定义主题或域时，可以为其启用系统分配的标识。 下图显示了如何为自定义主题启用系统托管标识。 基本上，你可以在主题创建向导的“高级”页面上选择“启用系统分配的标识”选项 。 在域创建向导的“高级”页上也可以看到此选项。 

![在创建自定义主题时启用标识](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
你还可以使用 Azure CLI 创建具有系统分配的标识的自定义主题或域。 使用 `az eventgrid topic create` 命令，将 `--identity` 参数设置为 `systemassigned`。 如果不为此参数指定值，则使用默认值 `noidentity`。 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

类似地，可以使用 `az eventgrid domain create` 命令创建具有系统托管标识的域。

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>为现有自定义主题或域启用标识
本部分介绍如何为现有的自定义主题或域启用系统托管标识。 

### <a name="using-azure-portal"></a>使用 Azure 门户
下面的过程展示了如何为自定义主题启用系统托管标识。 为域启用标识的步骤类似。 

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 在顶部的搜索栏中，搜索“事件网格主题”。
3. 选择要为其启用托管标识的自定义主题。 
4. 切换到“标识”选项卡。 
5. 打开启用标识的开关。 
1. 在工具栏上选择“保存”，保存该设置。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="自定义主题的“标识”页面"::: 

你可以使用类似的步骤为事件网格域启用标识。

### <a name="use-the-azure-cli"></a>使用 Azure CLI
使用 `az eventgrid topic update` 命令并将 `--identity` 设置为 `systemassigned`，为现有的自定义主题启用系统分配的标识。 如果要禁用标识，请将该值指定为 `noidentity`。 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

更新现有域的命令也是类似的 (`az eventgrid domain update`)。


## <a name="next-steps"></a>后续步骤
在目标（例如，服务总线队列）上将标识添加到相应角色（例如，服务总线数据发送方）。 有关详细步骤，请参阅[授予托管标识对事件网格目标的访问权限](add-identity-roles.md)。 
