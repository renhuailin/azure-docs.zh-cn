---
title: 对 Azure 事件网格自定义主题和域启用托管标识
description: 本文介绍如何为 Azure 事件网格自定义主题或域启用托管服务标识。
ms.topic: how-to
ms.date: 08/20/2021
ms.openlocfilehash: 9a63e6ae65c1348e22418506bb3bf475aafc0c10
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633524"
---
# <a name="assign-a-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>为事件网格自定义主题或域分配托管标识 
本文介绍如何为事件网格自定义主题或域分配系统分配或用户分配的标识。 若要了解托管标识，请参阅[什么是 Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

> [!IMPORTANT]
> 可以为事件网格主题或域启用系统分配的标识或用户分配的标识，但不能同时启用这两者。 最多可以将两个由用户分配的标识分配给主题或域。 

## <a name="enable-identity-when-creating-a-topic-or-domain"></a>创建主题或域时启用标识

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)
在 Azure 门户中创建自定义主题或域时，可以为其分配系统分配的标识或用户分配的标识。 

### <a name="enable-system-assigned-identity"></a>启用系统分配的标识
在主题或域创建向导的“高级”选项卡上，选择“启用系统分配的标识”。  

:::image type="content" source="./media/managed-service-identity/create-topic-identity.png" alt-text="显示选中了“启用系统分配的标识”选项的图像。":::

### <a name="enable-user-assigned-identity"></a>启用用户分配的标识
1. 在主题或域创建向导的“高级”页上，选择“启用用户分配的标识”，然后选择“添加用户分配的标识”。   

    :::image type="content" source="./media/managed-service-identity/create-page-add-user-assigned-identity-link.png" alt-text="显示选中了“启用用户分配的标识”选项的图像。":::
1. 在“选择用户分配的标识”窗口中，选择具有用户分配标识的订阅，选择“用户分配的标识”，然后单击“选择”。   

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)
还可以使用 Azure CLI 创建具有系统分配标识的自定义主题或域。 使用 `az eventgrid topic create` 命令，将 `--identity` 参数设置为 `systemassigned`。 如果不为此参数指定值，则使用默认值 `noidentity`。 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

类似地，可以使用 `az eventgrid domain create` 命令创建具有系统分配标识的域。

> [!NOTE]
> Azure CLI 尚不支持为事件网格主题或域分配用户分配的托管标识。 

---

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>为现有自定义主题或域启用标识
本部分介绍如何为现有的自定义主题或域启用系统分配的标识或用户分配的标识。 

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)
下面的过程展示了如何为自定义主题启用系统分配的标识。 为域启用标识的步骤类似。 

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 在顶部的搜索栏中，搜索“事件网格主题”。
3. 选择要为其启用托管标识的自定义主题。 
4. 在左侧菜单中选择“标识”。

### <a name="to-assign-a-system-assigned-identity-to-a-topic"></a>为主题分配系统分配的标识
1. 在“系统分配”选项卡中，打开开关以启用标识。  
1. 在工具栏上选择“保存”，保存该设置。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="自定义主题的“标识”页面"::: 

### <a name="to-assign-a-user-assigned-identity-to-a-topic"></a>为主题分配用户分配的标识
1. 按照[管理用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md)一文中的说明，创建用户分配的标识。 
1. 在“标识”页上，切换到右侧窗格中的“用户分配”选项卡，然后选择工具栏上的“+ 添加”。  

    :::image type="content" source="./media/managed-service-identity/user-assigned-identity-add-button.png" alt-text="显示“用户分配的标识”选项卡的图像":::     
1. 在“添加用户托管标识”窗口中，执行以下步骤：
    1. 选择包含用户分配标识的 Azure 订阅。 
    1. 选择用户分配的标识。 
    1. 选择 **添加** 。 
1. 刷新“用户分配”选项卡中的列表以查看刚刚添加的用户分配标识。

你可以使用类似的步骤为事件网格域启用标识。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)
使用 `az eventgrid topic update` 命令并将 `--identity` 设置为 `systemassigned`，为现有的自定义主题启用系统分配的标识。 如果要禁用标识，请将该值指定为 `noidentity`。 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

更新现有域的命令也是类似的 (`az eventgrid domain update`)。

> [!NOTE]
> Azure CLI 尚不支持为事件网格主题或域分配用户分配的托管标识。 

---

## <a name="next-steps"></a>后续步骤
在目标（例如，服务总线队列）上将标识添加到相应角色（例如，服务总线数据发送方）。 有关详细步骤，请参阅[授予托管标识对事件网格目标的访问权限](add-identity-roles.md)。 
