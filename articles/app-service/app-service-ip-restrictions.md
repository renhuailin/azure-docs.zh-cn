---
title: Azure 应用服务访问限制
description: 了解如何通过设置访问限制，在 Azure App Service 中保护应用。
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e1549dda367105db34272eab8a90c1760dd5bb5c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010174"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>设置 Azure App Service 访问限制

通过设置访问限制，可以定义按优先级排序的允许/拒绝列表，以控制对应用程序的网络访问。 该列表可以包含 IP 地址或 Azure 虚拟网络子网。 如果有一个或多个项，则在列表的末尾存在一个隐式 *拒绝* 。

访问限制功能适用于所有 Azure App Service 托管的工作负载。 工作负荷可以包括 web 应用、API 应用、Linux 应用、Linux 容器应用和功能。

向你的应用程序发出请求时，将根据你的访问限制列表中的 IP 地址规则来评估发件人地址。 如果 "发件人" 地址在配置了 "服务终结点到 Microsoft" 的子网中，则会将源子网与访问限制列表中的虚拟网络规则进行比较。 如果不允许根据列表中的规则访问该地址，服务会使用 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状态代码进行答复。

访问限制功能在应用服务前端角色中实现，这些角色是运行代码的辅助角色主机的上游。 因此，访问限制是 (Acl) 有效的网络访问控制列表。

[服务终结点][serviceendpoints]允许从 Azure 虚拟网络限制对 web 应用的访问。 使用服务终结点，你可以限制从所选子网对多租户服务的访问。 将流量限制到应用服务环境中托管的应用程序不起作用。 如果你使用的是应用服务环境，则可以通过应用 IP 地址规则来控制对应用的访问权限。

> [!NOTE]
> 必须同时在网络端和正在启用它们的 Azure 服务上启用服务终结点。 有关支持服务终结点的 Azure 服务的列表，请参阅 [虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。
>

![访问限制流关系图。](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>在门户中添加或编辑访问限制规则

若要向应用添加访问限制规则，请执行以下操作：

1. 登录到 Azure 门户。

1. 在左侧窗格中，选择 " **网络**"。

1. 在 " **网络** " 窗格的 " **访问限制**" 下，选择 " **配置访问限制**"。

   ![Azure 门户中的 "应用服务网络选项" 窗格的屏幕截图。](media/app-service-ip-restrictions/access-restrictions.png)  

1. 在 " **访问限制** " 页上，查看为应用定义的访问限制规则的列表。

   ![Azure 门户中的 "访问限制" 页的屏幕截图，其中显示了为所选应用定义的访问限制规则的列表。](media/app-service-ip-restrictions/access-restrictions-browse.png)

   此列表显示应用于应用的所有当前限制。 如果你的应用有虚拟网络限制，则该表将显示是否为 Microsoft 启用了服务终结点。 如果对应用未定义任何限制，则可以从任何位置访问该应用。  

### <a name="add-an-access-restriction-rule"></a>添加访问限制规则

若要向应用添加访问限制规则，请在 " **访问限制** " 窗格中选择 " **添加规则**"。 添加规则后，它将立即生效。 

规则按优先级顺序强制执行，从 " **优先级** " 列中的最小值开始。 添加甚至单个规则后，隐式 " *全部拒绝* " 会生效。

在 " **添加 IP 限制** " 窗格中创建规则时，请执行以下操作：

1. 在 " **操作**" 下，选择 " **允许** " 或 " **拒绝**"。  

   !["添加 IP 限制" 窗格的屏幕截图。](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. （可选）输入规则的名称和描述。  
1. 在 " **类型** " 下拉列表中，选择规则的类型。  
1. 在 " **优先级** " 框中，输入一个优先级值。  
1. 在 " **订阅**"、" **虚拟网络**" 和 " **子网** " 下拉列表中，选择要限制对其进行访问的内容。  

### <a name="set-an-ip-address-based-rule"></a>设置基于 IP 地址的规则

按照上一部分中所述的过程进行操作，但有以下差异：
* 在步骤3的 " **类型** " 下拉列表中，选择 " **IPv4** " 或 " **IPv6**"。 

为 IPv4 和 IPv6 地址指定无类别 Inter-Domain 路由 (CIDR) 表示法中的 IP 地址。 若要指定地址，可以使用 *1.2.3.4/32* 等内容，其中前四个八位字节表示你的 IP 地址， */32* 是掩码。 所有地址的 IPv4 CIDR 表示法都为 0.0.0.0/0。 若要了解有关 CIDR 表示法的详细信息，请参阅无 [类别 Inter-Domain 路由](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 

## <a name="use-service-endpoints"></a>使用服务终结点

使用服务终结点可以限制对所选 Azure 虚拟网络子网的访问。 若要限制对特定子网的访问，请使用 **虚拟网络** 类型创建限制规则。 然后，可以选择要允许或拒绝访问的订阅、虚拟网络和子网。 

如果未对所选子网启用服务终结点，则会自动启用这些终结点，除非选中 " **忽略缺少的 Microsoft Web 服务终结点** " 复选框。 你可能想要在应用程序上启用服务终结点而不是子网的方案主要取决于你是否有权在子网上启用它们。 

如果需要其他人在子网上启用服务终结点，请选中 " **忽略缺少的 Microsoft Web 服务终结点** " 复选框。 将为服务终结点配置应用，以便在以后的子网中启用它们。 

!["添加 IP 限制" 窗格的屏幕截图，其中选择了虚拟网络类型。](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

不能使用服务终结点限制对在应用服务环境中运行的应用程序的访问。 如果你的应用处于应用服务环境中，你可以通过应用 IP 访问规则来控制对它的访问。 

对于服务终结点，你可以配置应用程序网关或其他 web 应用程序防火墙 (WAF) 设备。 你还可以配置具有安全后端的多层应用程序。 有关详细信息，请参阅 [联网功能和应用服务](networking-features.md) 以及 [与服务终结点的应用程序网关集成](networking/app-gateway-with-service-endpoints.md)。

> [!NOTE]
> - 使用 IP 安全套接字层 (SSL) 虚拟 IP (VIP) 的 web 应用目前不支持服务终结点。
> - IP 或服务终结点限制为512行。 如果需要超过512行的限制，建议你考虑安装独立的安全产品，如 Azure 前门、Azure 应用网关或 WAF。
>

## <a name="manage-access-restriction-rules"></a>管理访问-限制规则

你可以编辑或删除现有的访问限制规则。

### <a name="edit-a-rule"></a>编辑规则

1. 若要开始编辑现有的访问限制规则，请在 " **访问限制** " 页上双击要编辑的规则。

1. 在 " **编辑 IP 限制** " 窗格中进行更改，然后选择 " **更新规则**"。 编辑会立即生效，包括优先级排序更改。

   ![Azure 门户中 "编辑 IP 限制" 窗格的屏幕截图，显示现有访问限制规则的字段。](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

   > [!NOTE]
   > 编辑规则时，无法在 IP 地址规则和虚拟网络规则之间进行切换。 

   ![Azure 门户中 "编辑 IP 限制" 窗格的屏幕截图，显示虚拟网络规则的设置。](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

### <a name="delete-a-rule"></a>删除规则

若要删除规则，请在 " **访问限制** " 页上，选择要删除的规则旁边的省略号 (**...**) ，然后选择 " **删除**"。

!["访问限制" 页的屏幕截图，显示要删除的访问限制规则旁边的 "删除" 省略号。](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>阻止单个 IP 地址

添加第一个 IP 限制规则时，服务将添加优先级为2147483647的显式 *拒绝全部* 规则。 在实践中，显式 " *全部拒绝* " 规则是要执行的最终规则，它会阻止访问 *允许* 规则未明确允许的任何 IP 地址。

对于想要显式阻止单个 IP 地址或 IP 地址块的方案，但允许访问其他任何内容，请添加 " *全部允许* " 规则。

![Azure 门户中的 "访问限制" 页的屏幕截图，显示单个阻止的 IP 地址。](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>限制对 SCM 站点的访问 

除了能够控制对应用程序的访问权限外，你还可以限制对你的应用程序使用的 SCM 网站的访问权限。 SCM 站点既是 web 部署终结点，也是 Kudu 控制台。 你可以单独向此应用分配对 SCM 站点的访问限制，或对应用和 SCM 站点使用相同的限制集。 选中 "**限制为 \<app name>** " 复选框时，所有内容都将被遮蔽。如果清除该复选框，则会重新应用 SCM 站点设置。 

![Azure 门户中的 "访问限制" 页的屏幕截图，显示没有为 SCM 站点或应用设置任何访问限制。](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>以编程方式管理访问限制规则

可以通过执行以下任一操作，以编程方式添加访问限制： 

* 使用 [Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true)。 例如：
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
  --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* 使用 [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true)。 例如：


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```

还可以通过执行下列任一操作来手动设置值：

* 对 Azure 资源管理器中的应用配置使用 [azure REST API](/rest/api/azure/) PUT 操作。 Azure 资源管理器中的此信息的位置如下：

  management.azure.com/subscriptions/subscription ID/resourceGroups/resource groups/providers/Microsoft.Web/sites/web app name/config/web?api-version=2018-02-01

* 使用 ARM 模板。 例如，可以使用 resources.azure.com 并编辑 ipSecurityRestrictions 块以添加所需的 JSON。

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

## <a name="set-up-azure-functions-access-restrictions"></a>设置 Azure Functions 访问限制

访问限制也适用于与应用服务计划具有相同功能的函数应用。 启用访问限制后，还会为任何不允许的 Ip 禁用 Azure 门户代码编辑器。

## <a name="next-steps"></a>后续步骤
[Azure Functions 的访问限制](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[应用程序网关与服务终结点的集成](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
