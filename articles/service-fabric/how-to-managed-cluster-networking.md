---
title: 配置 Service Fabric 托管群集的网络设置
description: 了解如何为 Service Fabric 托管群集配置 NSG 规则、RDP 端口访问、负载均衡规则，等等。
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: d953e9cd96c509a2410087588125b023613b380c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867354"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters"></a>配置 Service Fabric 托管群集的网络设置

Service Fabric 托管群集使用默认网络配置创建。 此配置包括一个使用公共 IP 的 [Azure 负载均衡器](../load-balancer/load-balancer-overview.md)、一个分配有一个子网的 VNet，以及一个已配置用于实现基本群集功能的 NSG。 此外还应用了旨在简化客户配置的可选 NSG 规则，例如默认允许所有出站流量。 本文档逐步介绍如何修改以下网络配置选项及其他配置：

- [管理 NSG 规则](#nsgrules)
- [管理 RDP 访问](#rdp)
- [管理负载均衡器配置](#lbconfig)
- [启用 IPv6](#ipv6)
- [自带虚拟网络](#byovnet)
- [自带负载均衡器](#byolb)

<a id="nsgrules"></a>
## <a name="manage-nsg-rules"></a>管理 NSG 规则

### <a name="nsg-rules-guidance"></a>NSG 规则指南

为托管群集创建新的 NSG 规则时，请注意以下几点。

* Service Fabric 托管群集将 NSG 规则的优先级范围保持在 0 到 999，以实现基本功能。 不能创建优先级值小于 1000 的自定义 NSG 规则。
* Service Fabric 托管群集为创建可选的 NSG 规则保留 3001 到 4000 的优先级范围。 将自动添加这些规则，以便快速轻松地进行配置。 可以通过添加优先级为 1000 到 3000 的自定义 NSG 规则来覆盖这些规则。
* 自定义 NSG 规则的优先级范围应在 1000 到 3000。

### <a name="apply-nsg-rules"></a>应用 NSG 规则
使用 Service Fabric 托管群集，可以在部署模板的群集资源中直接分配 NSG 规则。

使用 Microsoft.ServiceFabric/managedclusters 资源（版本 `2021-05-01` 或更高版本）的 [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) 属性来分配 NSG 规则。 例如：

```json
            "apiVersion": "2021-05-01",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound"
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound"
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

## <a name="clientconnection-and-httpgatewayconnection-default-and-optional-rules"></a>ClientConnection 和 HttpGatewayConnection 默认规则与可选规则
### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>NSG 规则：SFMC_AllowServiceFabricGatewayToSFRP

添加了一个默认 NSG 规则，以允许 Service Fabric 资源提供程序访问群集的 clientConnectionPort 和 httpGatewayConnectionPort。 此规则允许通过服务标记“ServiceFabric”访问端口。

>[!NOTE]
>此规则始终可以添加，但不能重写。

```json
{
    "name": "SFMC_AllowServiceFabricGatewayToSFRP",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.",
        "protocol": "TCP",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "ServiceFabric",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 500,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>NSG 规则：SFMC_AllowServiceFabricGatewayPorts

使用此可选规则，客户可访问 SFX，通过 PowerShell 连接到群集，以及通过开启 clientConnectionPort 和 httpGatewayPort 的 LB 端口使用 Internet 中的 Service Fabric 群集 API 终结点。

>[!NOTE]
>如果自定义规则对同一个端口具有相同的访问权限、方向和协议值，则不会添加该规则。 可以用自定义 NSG 规则替代此规则。

```json
{
    "name": "SFMC_AllowServiceFabricGatewayPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3001,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

<a id="rdp"></a>
## <a name="enable-access-to-rdp-ports-from-internet"></a>允许从 Internet 访问 RDP 端口

默认情况下，Service Fabric 托管群集不允许从 Internet 对 RDP 端口进行入站访问。 可以通过在 Service Fabric 托管群集资源上设置以下属性来打开从 Internet 对 RDP 端口的入站访问。

```json
"allowRDPAccess": true
```

当 allowRDPAccess 属性设置为 true 时，以下 NSG 规则将添加到群集部署中。

```json
{
    "name": "SFMC_AllowRdpPort",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

Service Fabric 托管群集会为节点类型中的每个实例自动创建入站 NAT 规则。 若要查找端口映射以访问特定实例（群集节点），请执行以下步骤：

使用 Azure 门户，找到为远程桌面协议 (RDP) 创建入站 NAT 规则的托管群集。

1. 在采用以下格式命名的订阅中，导航到此托管群集资源组：SFC_{cluster-id}

2. 为以下格式的群集选择负载均衡器：LB-{cluster-name}

3. 在负载均衡器页上，选择“入站 NAT 规则”。 查看入站 NAT 规则，以确认节点目标端口映射的入站前端端口。 

   以下屏幕截图显示三种不同节点类型的入站 NAT 规则：

   ![入站 NAT 规则][Inbound-NAT-Rules]

   默认情况下，对于 Windows 群集，前端端口在 50000 及更大范围内，目标端口是端口 3389，此端口已映射到目标节点的 RDP 服务。

4. 远程连接到特定节点（规模集实例）。 可以使用创建群集时设置的用户名和密码，也可使用已配置的其他任意凭据。

以下屏幕截图显示在 Windows 群集中使用远程桌面连接连接到应用（实例 0）节点：

![远程桌面连接][sfmc-rdp-connect]

<a id="lbconfig"></a>
## <a name="modify-default-load-balancer-configuration"></a>修改默认负载均衡器配置

### <a name="load-balancer-ports"></a>负载均衡器端口

Service Fabric 托管群集可在默认优先级范围内，为在“ManagedCluster”属性的“loadBalancingRules”部分所配置的所有负载均衡器 (LB) 端口创建 NSG 规则。 此规则为来自 Internet 的入站流量打开 LB 端口。  

>[!NOTE]
>此规则将添加到可选的优先级范围，并可通过添加自定义 NSG 规则进行重写。

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>负载均衡器探测

Service Fabric 托管群集为 Fabric 网关端口以及在托管群集属性的 `loadBalancingRules` 部分下配置的所有端口自动创建负载均衡器探测。

```json
{
  "value": [
    {
        "name": "FabricTcpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19000,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "FabricHttpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "probe1_tcp_8080",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 8080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
            {
                "id": "<>"
            }
        ]
      },
      "type": "Microsoft.Network/loadBalancers/probes"
    }
  ]
}
```

<a id="ipv6"></a>
## <a name="enable-ipv6-preview"></a>启用 IPv6（预览版）
托管群集默认不启用 IPv6。 此功能将启用从负载均衡器前端到后端资源的完整双堆栈 IPv4/IPv6 功能。 对托管群集负载均衡器配置或 NSG 规则进行的任何更改都将影响 IPv4 和 IPv6 路由。

> [!NOTE]
> 此项设置不会在门户中提供，并且在创建群集后无法更改

1. 在 Service Fabric 托管群集资源上设置以下属性。
   ```json
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                "enableIpv6": true
                },
            }
   ```

2. 部署已启用 IPv6 的托管群集。 根据需要自定义[示例模板](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/SF-Managed-Standard-SKU-2-NT-IPv6/AzureDeploy.json)，或生成你自己的模板。
   在以下示例中，我们将在 `westus` 中创建名为 `MyResourceGroup` 的资源组，并部署启用了此功能的群集。
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```
   部署后，你的群集虚拟网络和资源将是双堆栈的。 因此，将为群集前端负载均衡器创建唯一的 DNS 地址，例如，与 Azure 负载均衡器上的公共 IPv6 地址和 VM 上的专用 IPv6 地址关联的 `mycluster-ipv6.southcentralus.cloudapp.azure.com`。 


<a id="byovnet"></a>
## <a name="bring-your-own-virtual-network-preview"></a>自带虚拟网络（预览版）
此功能允许客户通过指定托管群集要将其资源部署到的专用子网来使用现有虚拟网络。 如果已经配置了具有相关安全策略和要使用的流量路由的 VNet 与子网，则此功能可能非常有用。 部署到现有虚拟网络后，可以轻松地使用或整合其他网络功能，例如 Azure ExpressRoute、Azure VPN 网关、网络安全组和虚拟网络对等互连。 此外，还可以根据需要[自带 Azure 负载均衡器](#byolb)。

> [!NOTE]
> 创建群集后无法更改此设置，托管群集会将 NSG 分配到提供的子网。 不要替代 NSG 分配，否则流量可能会中断。

若要自带虚拟网络，请执行以下操作：

1. 从 Service Fabric 资源提供程序应用程序的订阅中获取服务 `Id`。
   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > 确保你位于正确的订阅中，如果订阅位于其他租户中，则主体 ID 将更改。

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   请记下上面的输出中的 Id，它将在后面的步骤中用作 principalId 

   |角色定义名称|角色定义 ID|
   |----|-------------------------------------|
   |网络参与者|4d97b98b-1d4f-4787-a291-c67834d212e7|

   请记下 `Role definition name` 和 `Role definition ID` 属性值，以便在后面的步骤中使用

2. 将角色分配添加到 Service Fabric 资源提供程序应用程序。 添加角色分配是一次性的操作。 通过根据下面的详述运行以下 PowerShell 命令或配置 Azure 资源管理器 (ARM) 模板来添加角色。 

   在以下步骤中，我们从 ExistingRG 资源组中名为 ExistingRG-vnet 的现有虚拟网络开始。 子网命名为 default。

   从现有 VNet 获取所需的信息。

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzVirtualNetwork -Name ExistingRG-vnet -ResourceGroupName ExistingRG
   ```
   请记下以下子网名称以及在响应的 `Subnets` 节中返回的 `Id` 属性值，因为在后续步骤中需要用到。

   ```JSON
   Subnets:[
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/virtualNetworks/ExistingRG-vnet/subnets/default"
   }]
   ```

   使用在步骤 2 中记下的主体 ID、角色定义名称以及前面获取的分配范围 `Id` 运行以下 PowerShell 命令：
   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
   ```

   或者，可以通过已使用 `principalId`、`roleDefinitionId`、`vnetName` 和 `subnetName` 的属性值配置的 Azure 资源管理器 (ARM) 模板来添加角色分配：

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('VNetRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > VNetRoleAssignmentID 必须是一个 [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16)。 如果再次部署包含此角色分配的模板，请确保该 GUID 与最初使用的 GUID 相同。 建议单独运行此代码，或者在部署后从群集模板中删除此资源，因为它只需创建一次。

   下面是完整的示例 [Azure 资源管理器 (ARM) 模板，它可以创建 VNet 子网，并执行可用于此步骤的角色分配](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/SF-Managed-Standard-SKU-2-NT-BYOVNET/SFMC-VNet-RoleAssign.json)。

3. 设置角色后，按下面所示配置群集部署的 `subnetId` 属性：

   ```JSON
    "resources": [
        {
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            },
            "properties": {
                "subnetId": "subnetId",
            ...
            }
   ```
   参阅[自带 VNet 群集示例模板](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/SF-Managed-Standard-SKU-2-NT-BYOVNET/AzureDeploy.json)或自定义你自己的模板。

4. 部署配置的托管群集 Azure 资源管理器 (ARM) 模板。

   在以下示例中，我们将在 `westus` 中创建名为 `MyResourceGroup` 的资源组，并部署启用了此功能的群集。
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   如果你自带 VNet 子网，公共终结点仍由资源提供程序创建和管理，但位于配置的子网中。 该功能不允许在 Azure 负载均衡器上指定公共 IP/重复使用静态 IP。 你可以[自带 Azure 负载均衡器](#byolb)以便与此功能配合使用；如果所需的其他负载均衡器方案原生不受支持，你可以单独使用此功能。

<a id="byolb"></a>
## <a name="bring-your-own-azure-load-balancer-preview"></a>自带 Azure 负载均衡器（预览版）
托管群集使用一个用于主要和辅助节点类型的静态公共 IP 创建 Azure 负载均衡器和完全限定的域名。 使用此功能可为辅助节点类型创建或重复使用 Azure 负载均衡器来传送入站和出站流量。 自带 Azure 负载均衡器时，可以：

* 使用预配置的负载均衡器静态 IP 地址传送专用或公共流量
* 将负载均衡器映射到特定的节点类型
* 为每个节点类型配置 NSG 规则，因为每个节点类型将部署在其自己的 VNET 中
* 维护可能已实施的现有策略和控制措施

> [!NOTE]
> 为某个节点类型完成群集部署后，无法从默认配置切换到自定义配置，但部署后可以修改自定义负载均衡器配置。

功能要求
 * 支持基本和标准 SKU Azure 负载均衡器类型
 * 现有 Azure 负载均衡器上必须配置后端和 NAT 池。 请参阅[此处有关创建和分配角色的完整示例](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/SF-Managed-Standard-SKU-2-NT-BYOLB/createlb-and-assign-role)。 

下面是客户可以使用此功能的几个示例场景：

在此示例中，客户想要通过已使用现有静态 IP 地址配置的现有 Azure 负载均衡器将流量路由到两个节点类型。
![自带负载均衡器示例 1][sfmc-byolb-example-1]

在此示例中，客户想要通过现有的 Azure 负载均衡器路由流量，以帮助他们单独管理发往独立节点类型上的应用程序的流量流。 按此示例所示进行设置时，每个节点类型将位于其自己的、可由你管理的 NSG 后面。
![自带负载均衡器示例 2][sfmc-byolb-example-2]

若要配置自带负载均衡器，请执行以下操作：

1. 从 Service Fabric 资源提供程序应用程序的订阅中获取服务 `Id`：

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > 确保你位于正确的订阅中，如果订阅位于其他租户中，则主体 ID 将更改。

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   请记下上面的输出中的 Id，它将在后面的步骤中用作 principalId 

   |角色定义名称|角色定义 ID|
   |----|-------------------------------------|
   |网络参与者|4d97b98b-1d4f-4787-a291-c67834d212e7|

   请记下 `Role definition name` 和 `Role definition ID` 属性值，以便在后面的步骤中使用

2. 将角色分配添加到 Service Fabric 资源提供程序应用程序。 添加角色分配是一次性的操作。 通过根据下面的详述运行以下 PowerShell 命令或配置 Azure 资源管理器 (ARM) 模板来添加角色。

   在以下步骤中，我们将从 Existing-RG 资源组中名为 Existing-LoadBalancer1 的现有负载均衡器开始。 子网命名为 default。

   从现有 Azure 负载均衡器获取所需的 `Id` 属性信息。 我们将 

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzLoadBalancer -Name "Existing-LoadBalancer1" -ResourceGroupName "Existing-RG"
   ```
   请记下下面的 `Id`，因为下一步骤中需要用到它：
   ```JSON
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/loadBalancers/Existing-LoadBalancer1"
   }
   ```
   使用在步骤 2 中记下的主体 ID、角色定义名称以及刚刚获取的分配范围 `Id` 运行以下 PowerShell 命令：

   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/loadBalancers/<LoadBalancerName>"
   ```

   或者，可以通过已使用 `principalId`、`roleDefinitionId`、`vnetName` 和 `subnetName` 的属性值配置的 Azure 资源管理器 (ARM) 模板来添加角色分配：

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('loadBalancerRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > loadBalancerRoleAssignmentID 必须是一个 [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16)。 如果再次部署包含此角色分配的模板，请确保该 GUID 与最初使用的 GUID 相同。 建议单独运行此代码，或者在部署后从群集模板中删除此资源，因为它只需创建一次。

3. 配置所需的出站连接。 所有节点必须能够将端口 443 上的出站流量路由到 ServiceFabric 资源提供程序。 可以在 NSG 中使用 `ServiceFabric` 服务标记将流量目标限制为 Azure 终结点。

4. （可选）在现有 Azure 负载均衡器上配置入站应用程序端口和相关探测。

5. （可选）配置应用于节点类型的托管群集 NSG 规则，以允许 Azure 负载均衡器上配置的任何所需流量，否则会阻止流量。

   有关如何打开入站规则的示例，请参阅[自带负载均衡器示例 Azure 资源管理器 (ARM) 模板](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/SF-Managed-Standard-SKU-2-NT-BYOLB/AzureDeploy.json)。

6. 部署已配置的托管群集 ARM 模板

   在以下示例中，我们将在 `westus` 中创建名为 `MyResourceGroup` 的资源组，并部署启用了此功能的群集。
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   部署后，辅助节点类型将配置为使用指定的负载均衡器传送入站和出站流量。 Service Fabric 客户端连接和网关终结点仍将指向托管群集主要节点类型静态 IP 地址的公共 DNS。


## <a name="next-steps"></a>后续步骤
[Service Fabric 托管群集配置选项](how-to-managed-cluster-configuration.md)
[Service Fabric 托管群集概述](overview-managed-cluster.md)

<!--Image references-->
[Inbound-NAT-Rules]: ./media/how-to-managed-cluster-networking/inbound-nat-rules.png
[sfmc-rdp-connect]: ./media/how-to-managed-cluster-networking/sfmc-rdp-connect.png
[sfmc-byolb-example-1]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-1.png
[sfmc-byolb-example-2]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-2.png

