---
title: 为 Azure 服务总线配置虚拟网络服务终结点
description: 本文提供了有关如何向虚拟网络中添加 Microsoft.ServiceBus 服务终结点的信息。
ms.topic: article
ms.date: 03/29/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 63a2f556739f7f3eaec3874e6d02cc996e2aaec4
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112416715"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>允许从特定虚拟网络访问 Azure 服务总线命名空间
通过将服务总线与[虚拟网络 (VNet) 服务终结点][vnet-sep]集成可从绑定到虚拟网络的工作负荷（如虚拟机）安全地访问消息传递功能，同时在两端保护网络流量路径。

配置为绑定到至少一个虚拟网络子网服务终结点后，相应的服务总线命名空间将不再接受授权虚拟网络以及特定 Internet IP 地址（可选）以外的任何位置的流量。 从虚拟网络的角度来看，通过将服务总线命名空间绑定到服务终结点，可配置从虚拟网络子网到消息传递服务的独立网络隧道。

然后，绑定到子网的工作负荷与相应的服务总线命名空间之间将存在专用和独立的关系，消息传递服务终结点的可观察网络地址位于公共 IP 范围内对此没有影响。

## <a name="important-points"></a>要点
- 虚拟网络仅在[高级层](service-bus-premium-messaging.md)服务总线命名空间中受支持。 将 VNet 服务终结点用于服务总线时，不应在混合使用标准层和高级层服务总线命名空间的应用程序中启用这些终结点。 原因是标准层不支持 VNet。 此终结点仅限于高级层命名空间。
- 实现虚拟网络集成可以防止其他 Azure 服务与服务总线交互。 例外情况是，即使启用了网络服务终结点，也可以允许从某些“受信任的服务”访问服务总线资源。 有关受信任服务的列表，请参阅[受信任服务](#trusted-microsoft-services)。

    以下 Microsoft 服务必须在虚拟网络中
    - Azure 应用服务
    - Azure Functions
- 为命名空间指定至少一个 IP 规则或虚拟网络规则，以便仅允许来自虚拟网络的指定 IP 地址或子网的流量。 如果没有 IP 和虚拟网络规则，则可以通过公共 Internet（使用访问密钥）访问命名空间。  

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>通过 VNet 集成启用的高级安全方案 

对于需要严格和隔离安全性的解决方案和虚拟网络子网在其中的隔离服务之间提供分段的解决方案，它们通常仍然需要驻留在这些隔离舱中的服务之间的通信路径。

隔离舱之间的任何即时 IP 路由（包括通过 TCP/IP 承载 HTTPS 的）都存在利用网络层漏洞的风险。 消息传递服务提供完全隔离的通信路径，其中消息在各方之间转换时会以平均方式写入磁盘。 绑定到同一个服务总线实例的两个不同虚拟网络中的工作负荷可通过消息进行高效和可靠的通信，同时保留各自的网络隔离边界完整性。
 
这意味着安全敏感云解决方案不仅可以访问 Azure 行业领先的可靠且可扩展的异步消息传递功能，而且现在可以使用消息传递在安全解决方案隔离舱之间创建通信路径，这些隔离舱本质上比利用任何对等通信模式（包括 HTTPS 和其他 TLS 安全套接字协议）更加安全。

## <a name="binding-service-bus-to-virtual-networks"></a>将服务总线绑定到虚拟网络

虚拟网络规则是一种防火墙安全功能，用于控制是否允许 Azure 服务总线服务器接受来自特定虚拟网络子网的连接。

将服务总线命名空间绑定到虚拟网络的过程分为两步。 首先需要在虚拟网络子网上创建“虚拟网络服务终结点”，并按照[服务终结点概述][vnet-sep]中的说明为“Microsoft.ServiceBus”启用该终结点 。 添加服务终结点后，使用虚拟网络规则将服务总线命名空间绑定到该终结点。

虚拟网络规则是服务总线命名空间与虚拟网络子网的关联。 存在此规则时，绑定到子网的所有工作负荷都有权访问服务总线命名空间。 服务总线本身永远不会建立出站连接，不需要获得访问权限，因此永远不会通过启用此规则来授予对子网的访问权限。

> [!NOTE]
> 请记住，网络服务终结点为在虚拟网络中运行的应用程序提供对服务总线命名空间的访问权限。 虚拟网络控制终结点的可访问性，但不能控制对服务总线实体（队列、主题或订阅）可以执行的操作。 使用 Azure Active Directory (Azure AD) 授权应用程序可以在命名空间及其实体上执行的操作。 若要详细了解，请参阅[使用 Azure AD 对应用程序进行身份验证和授权，使之能够访问服务总线实体](authenticate-application.md)。


## <a name="use-azure-portal"></a>使用 Azure 门户
本部分演示如何使用 Azure 门户添加虚拟网络服务终结点。 若要限制访问，需要集成此事件中心命名空间的虚拟网络服务终结点。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到“服务总线命名空间”。
2. 在左侧菜单上，选择“设置”下的“网络”选项 。  

    > [!NOTE]
    > 只会为“高级”命名空间显示“网络”选项卡 。  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="网络页面 - 默认" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    如果你选择“所有网络”选项，你的服务总线命名空间将接受来自 IP 地址的连接。 此默认设置等效于接受 0.0.0.0/0 IP 地址范围的规则。 

    ![防火墙 - 选中了“所有网络”选项](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. 若要限制对特定虚拟网络的访问，请选择“选定网络”选项（如果尚未选择）。
1. 在页面的“虚拟网络”部分，选择“+添加现有虚拟网络” 。 

    ![添加现有虚拟网络](./media/service-endpoints/add-vnet-menu.png)

    >[!WARNING]
    > 如果你在此页上选择了“所选网络”选项并且未添加至少一个 IP 防火墙规则或虚拟网络，则可以通过公共 Internet（使用访问密钥）访问该命名空间。
3. 从虚拟网络列表中选择虚拟网络，然后选择“子网”。 将虚拟网络添加到列表之前，必须启用服务终结点。 如果未启用服务终结点，门户将提示启用。
   
   ![选择子网](./media/service-endpoints/select-subnet.png)

4. 为 Microsoft.ServiceBus 启用子网的服务终结点后，应显示指示启用成功的以下消息。 选择页面底部的“添加”，添加网络。 

    ![选择子网并启用终结点](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > 如果无法使用资源管理器模板启用服务终结点，可以忽略有关缺少虚拟网络服务终结点的消息。 此功能在门户中不可用。
6. 在工具栏上选择“保存”，保存这些设置。 等待几分钟，直到门户通知中显示确认消息。 应禁用“保存”按钮。 

    ![保存网络](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > 有关允许从特定 IP 地址或范围访问的说明，请参阅[允许从特定 IP 地址或范围访问](service-bus-ip-filtering.md)。

[!INCLUDE [service-bus-trusted-services](./includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>使用 Resource Manager 模板
以下示例资源管理器模板会向现有服务总线命名空间添加虚拟网络规则。 对于网络规则，该模板指定虚拟网络中子网的 ID。 

ID 是虚拟网络子网的完全限定资源管理器路径。 例如，`/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` 表示虚拟网络的默认子网。

添加虚拟网络或防火墙规则时，请将 `defaultAction` 的值设置为 `Deny`。

模板：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

若要部署模板，请按照 [Azure 资源管理器][lnk-deploy]的说明进行操作。

> [!IMPORTANT]
> 如果没有 IP 和虚拟网络规则，则所有流量都将流向命名空间，即使你将 `defaultAction` 设置为 `deny`。  可以通过公共 Internet（使用访问密钥）访问命名空间。 为命名空间指定至少一个 IP 规则或虚拟网络规则，以便仅允许来自虚拟网络的指定 IP 地址或子网的流量。  

## <a name="next-steps"></a>后续步骤

有关虚拟网络的详细信息，请参阅以下链接：

- [Azure 虚拟网络服务终结点][vnet-sep]
- [Azure 服务总线 IP 筛选][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
