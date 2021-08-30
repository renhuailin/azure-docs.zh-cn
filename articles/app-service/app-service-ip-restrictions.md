---
title: Azure 应用服务访问限制
description: 了解如何通过设置访问限制在 Azure 应用服务中保护应用。
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: afadabb0503cde997858fce1ff9d1e82ac6d2f89
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112679513"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>设置 Azure 应用服务访问限制

通过设置访问限制可以定义一个按优先级排序的允许/拒绝列表，用于控制在网络中对应用的访问。 该列表可以包含 IP 地址或 Azure 虚拟网络子网。 如果存在一个或多个条目，则列表末尾会存在一个隐式的“全部拒绝”。

访问限制功能适用于所有 Azure 应用服务托管的工作负载。 工作负载可包括 Web 应用、API 应用、Linux 应用、Linux 容器应用和函数。

向应用发出请求时，会根据访问限制列表中的规则评估 FROM 地址。 如果 FROM 地址位于配置为使用 Microsoft.Web 服务终结点的子网中，则会根据访问限制列表中的虚拟网络规则比较源子网。 如果根据列表中的规则不允许该地址进行访问，则服务会返回 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状态码。

访问限制功能是在应用服务前端角色（即代码运行所在的辅助角色主机中的上游）中实现的。 因此，访问限制是有效的网络访问控制列表 (ACL)。

限制从 Azure 虚拟网络访问 Web 应用的功能通过[服务终结点][serviceendpoints]启用。 使用服务终结点，可以限制从选定的子网对多租户服务进行访问。 对托管在应用服务环境中的应用的流量进行限制不起作用。 如果处于应用服务环境中，可应用 IP 地址规则控制对应用的访问。

> [!NOTE]
> 必须在网络端以及要对其启用服务终结点的 Azure 服务上同时启用服务终结点。 有关支持服务终结点的 Azure 服务列表，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="访问限制流程图。":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>在门户中管理访问限制规则

若要向应用添加访问限制规则，请执行以下操作：

1. 登录到 Azure 门户。

1. 在左窗格中，选择“网络”。

1. 在“网络”窗格的“访问限制”下，选择“配置访问限制”  。

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Azure 门户中的“应用服务网络选项”窗格的屏幕截图。":::

1. 在“访问限制”页上查看为应用定义的访问限制规则列表。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Azure 门户中“访问限制”页面的屏幕截图，显示为所选应用定义的访问限制规则的列表。":::

   列表显示当前应用于该应用的所有限制。 如果存在对应用的虚拟网络限制，该表将显示是否为 Microsoft.Web 启用了服务终结点。 如果未对应用定义任何限制，则可从任何位置访问该应用。

### <a name="add-an-access-restriction-rule"></a>添加访问限制规则

若要向应用添加访问限制规则，请在“访问限制”窗格中选择“添加规则” 。 规则在添加后会立即生效。 

规则会从“优先级”列中最小的数字开始，按优先级顺序强制执行。 即使只添加了一条规则，一个隐式的“全部拒绝”也会立即生效。

在“添加访问限制”窗格上创建规则时，请执行以下操作：

1. 在“操作”下，选择“允许”或“拒绝”  。  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="“添加访问限制”窗格的屏幕截图。":::

1. （可选）输入规则名称和说明。
1. 在“优先级”框中，输入一个优先级值。
1. 在“类型”下拉列表中选择规则类型。

将在下面各部分介绍这些类型的规则。

> [!NOTE]
> - 限制为 512 条访问限制规则。 如果需要的访问限制规则数超出 512 条，建议考虑安装独立的安全产品，如 Azure Front Door、Azure 应用程序网关或替换 WAF。
>
#### <a name="set-an-ip-address-based-rule"></a>设置基于 IP 地址的规则

按照上一部分所述的过程操作，但有以下新增内容：
* 在步骤 4 的“类型”下拉列表中，选择“IPv4”或“IPv6”  。 

以无类别域际路由选择 (CIDR) 表示法为 IPv4 和 IPv6 地址指定 IP 地址块。 若要指定地址，可以使用类似 1.2.3.4/32 的格式，其中前四个八位字节代表自己的 IP 地址，/32 为掩码 。 所有地址的 IPv4 CIDR 表示法都为 0.0.0.0/0。 若要详细了解 CIDR 表示法，请参阅[无类别域际路由选择](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 

#### <a name="set-a-service-endpoint-based-rule"></a>设置基于服务终结点的规则

* 在步骤 4 的“类型”下拉列表中，选择“虚拟网络” 。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="“添加限制”窗格的屏幕截图，其中“虚拟网络类型”处于选中状态。":::

指定“订阅”、“虚拟网络”和“子网”下拉列表，以匹配要限制访问的内容  。

使用服务终结点可以限制对所选 Azure 虚拟网络子网的访问。 如果尚未为所选子网的 Microsoft.Web 启用服务终结点，它们会自动启用，除非你选择了“忽略缺少的 Microsoft.Web 服务终结点”复选框。 在应用上而不是子网上启用服务终结点的方案主要取决于你是否有权在子网上启用它们。 

如果需要其他人在子网上启用服务终结点，请选择“忽略缺少的 Microsoft.Web 服务终结点”复选框。 将为服务终结点配置应用，以便稍后在子网中启用它们。 

不能使用服务终结点来限制对在应用服务环境中运行的应用程序的访问。 当应用处于应用服务环境中时，可以应用 IP 访问规则来控制对它的访问。 

借助服务终结点，可以使用应用程序网关或其他 Web 应用程序防火墙 (WAF) 设备来配置应用。 你还可以配置具有安全后端的多层应用程序。 有关详细信息，请参阅[网络功能和应用服务](networking-features.md)和[应用程序网关与服务终结点的集成](networking/app-gateway-with-service-endpoints.md)。

> [!NOTE]
> - 对于通过虚拟 IP (VIP) 使用基于 IP 的 TLS/SSL 绑定的 Web 应用，当前不支持服务终结点。
>
#### <a name="set-a-service-tag-based-rule"></a>设置基于服务标记的规则

* 在步骤 4 的“类型”下拉列表中，选择“服务标记”。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="“添加限制”窗格的屏幕截图，其中“服务标记类型”处于选中状态。":::

每个服务标记代表 Azure 服务中的 IP 范围列表。 这些服务的列表以及指向特定范围的链接可以在[服务标记文档][servicetags]中找到。

访问限制规则支持所有可用服务标记。 为简单起见，只能通过 Azure 门户获得最常见标记的列表。 使用 Azure 资源管理器模板或脚本配置更高级的规则，如区域范围内的规则。 可以通过 Azure 门户获取以下标记：

* ActionGroup
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>编辑规则

1. 若要开始编辑现有的访问限制规则，请在“访问限制”页上选择要编辑的规则。

1. 在“编辑访问限制”窗格上，进行更改，然后选择“更新规则” 。 编辑的内容会立即生效，包括在优先级排序方面的更改。

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Azure 门户中“编辑访问限制”窗格的屏幕截图，其中显示现有访问限制规则的字段。":::

   > [!NOTE]
   > 编辑规则时，不能在规则类型之间切换。 

### <a name="delete-a-rule"></a>删除规则

若要删除规则，请在“访问限制”页上选择要删除的规则旁边的省略号 (...)，然后选择“删除”  。

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="“访问限制”页的屏幕截图，其中显示要删除的访问限制规则旁边的“删除”省略号。":::

## <a name="access-restriction-advanced-scenarios"></a>访问限制高级方案
以下各部分说明一些使用访问限制的高级方案。

### <a name="filter-by-http-header"></a>按 http 标头筛选

作为任何规则的一部分，你可以添加其他 http 标头筛选器。 支持以下 http 标头：
* X-Forwarded-For
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

对于每个标头名称，最多可以添加 8 个以逗号分隔的值。 在规则本身之后评估 http 标头筛选器，并且必须满足两个条件才能应用规则。

### <a name="multi-source-rules"></a>多源规则

多源规则允许在单个规则中合并多达 8 个 IP 范围或 8 个服务标记。 如果你有 512 个以上的 IP 范围，或者想要创建将多个 IP 范围与单个 http 标头筛选器组合在一起的逻辑规则，则可以使用此项。

多源规则定义方式与单源规则的定义方式相同，但每个范围用逗号分隔。

PowerShell 示例：

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>阻止单个 IP 地址

添加第一个访问限制规则时，服务将添加优先级为 2147483647 的显式“全部拒绝”规则。 实际上，显式“全部拒绝”规则将是最后执行的规则，并将阻止访问未被“允许”规则明确允许的任何 IP 地址 。

如果你希望显式阻止单个 IP 地址或 IP 地址块，但允许所有其他访问，请添加一个显式的“全部允许”规则。

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Azure 门户中的“访问限制”页的屏幕截图，只显示一个受阻止的 IP 地址。":::

### <a name="restrict-access-to-an-scm-site"></a>限制对 SCM 站点的访问 

除了能够控制对应用的访问以外，还可以限制对应用所用的 SCM 站点的访问。 SCM 站点既是 Web 部署终结点，也是 Kudu 控制台。 对于 SCM 站点，可以分配不同于应用的访问限制；也可以对应用和 SCM 站点使用相同的限制设置。 如果选择“与 \<app name> 相同的限制”复选框，则所有内容都将作废。如果清除该复选框，则会重新应用 SCM 站点设置。 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Azure 门户中“访问限制”页的屏幕截图，显示没有为 SCM 站点或应用设置访问限制。":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>限制对特定 Azure Front Door 实例的访问
从 Azure Front Door 流向应用程序的流量始发自 AzureFrontDoor.Backend 服务标记中定义的一组已知 IP 范围。 使用服务标记限制规则，可以限制流量只能来自 Azure Front Door。 为确保流量仅来自特定实例，需要根据 Azure Front Door 发送的唯一 http 标头进一步筛选传入的请求。

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png?v2" alt-text="Azure 门户中的“访问限制”页的屏幕截图，显示如何添加 Azure Front Door 限制。":::

PowerShell 示例：

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>以编程方式管理访问限制规则

可通过以下任一方法以编程方式添加访问限制： 

* 使用 [Azure CLI](/cli/azure/webapp/config/access-restriction)。 例如：
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

   > [!NOTE]
   > 在 Azure CLI 中使用服务标记、http 标头或多源规则至少需要版本 2.23.0。 可以使用以下命令来验证已安装模块的版本：```az version```

* 使用 [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule)。 例如：


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > 在 Azure PowerShell 中使用服务标记、http 标头或多源规则至少需要版本 5.7.0。 可以使用以下命令验证已安装模块的版本：```Get-InstalledModule -Name Az```

还可通过以下任一方法手动设置值：

* 在 Azure 资源管理器中对应用配置使用 [Azure REST API](/rest/api/azure/) PUT 操作。 此信息在 Azure 资源管理器中的位置为：

  management.azure.com/subscriptions/subscription ID/resourceGroups/resource groups/providers/Microsoft.Web/sites/web app name/config/web?api-version=2020-06-01  

* 使用资源管理器模板。 例如，可以使用 resources.azure.com 并编辑 ipSecurityRestrictions 块以添加所需的 JSON。

  前面的示例的 JSON 语法为：

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```
  使用服务标记和 http 头限制的高级示例的 JSON 语法为：
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
        }
      ]
    }
  }
  ```
## <a name="set-up-azure-functions-access-restrictions"></a>设置 Azure Functions 访问限制

访问限制也适用于与应用服务计划具有相同功能的函数应用。 启用访问限制时，还可针对任何不允许的 IP 禁用 Azure 门户代码编辑器。

## <a name="next-steps"></a>后续步骤
[Azure Functions 的访问限制](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[应用程序网关与服务终结点的集成](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md
