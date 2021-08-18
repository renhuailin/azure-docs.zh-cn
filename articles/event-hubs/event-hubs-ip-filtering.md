---
title: Azure 事件中心防火墙规则 | Microsoft Docs
description: 使用防火墙规则允许从特定 IP 地址到 Azure 事件中心的链接。
ms.topic: article
ms.date: 05/10/2021
ms.openlocfilehash: eb8f83d03fffe514fcd34a394943d4a0fef27c0c
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112417111"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>允许从特定 IP 地址或范围访问 Azure 事件中心命名空间
默认情况下，只要请求附带有效的身份验证和授权，就可以从 Internet 访问事件中心命名空间。 有了 IP 防火墙，就可以使用 [CIDR（无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法将其进一步限制为仅一组 IPv4 地址或 IPv4 地址范围。

在仅应从某些知名站点访问 Azure 事件中心的情况下，此功能很有用。 可使用防火墙规则配置相关规则，以接受源自特定 IPv4 地址的流量。 例如，如果将事件中心与 [Azure Express Route][express-route] 配合使用，则可创建防火墙规则，仅允许来自本地基础结构 IP 地址的流量。 

## <a name="ip-firewall-rules"></a>IP 防火墙规则
IP 防火墙规则应用于事件中心命名空间级别。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 如果某 IP 地址与事件中心命名空间上的允许 IP 规则不匹配，则将拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。 IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。


## <a name="important-points"></a>要点
- 基本层不支持此功能。
- 默认情况下，除非请求源自从允许的公共 IP 地址运行的服务，否则，为事件中心命名空间启用防火墙规则会阻止传入请求。 被阻止的请求包括来自其他 Azure 服务、来自 Azure 门户、来自日志记录和指标服务等的请求。 例外情况是，可以允许从某些受信任的服务访问事件中心资源，即使在启用了 IP 筛选功能时也是如此。 有关受信任服务的列表，请参阅[受信任的 Microsoft 服务](#trusted-microsoft-services)。
- 为命名空间指定至少一个 IP 防火墙规则或虚拟网络规则，以便仅允许来自虚拟网络的指定 IP 地址或子网的流量。 如果没有 IP 和虚拟网络规则，则可以通过公共 Internet（使用访问密钥）访问命名空间。  


## <a name="use-azure-portal"></a>使用 Azure 门户
本部分演示如何使用 Azure 门户为事件中心命名空间创建 IP 防火墙规则。 

1. 在 [Azure 门户](https://portal.azure.com)中导航到“事件中心命名空间”。
4. 在左侧“设置”下选择“网络” 。 
    
    > [!WARNING]
    > 如果你在此页上选择了“所选网络”选项并且未添加至少一个 IP 防火墙规则或虚拟网络，则可以通过公共 Internet（使用访问密钥）访问该命名空间。   

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="网络选项卡 -“所选网络”选项" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    如果选择“所有网络”选项，则事件中心接受来自任何 IP 地址的连接（使用访问密钥）。 此设置等效于一个接受 0.0.0.0/0 IP 地址范围的规则。 

    ![显示“防火墙和虚拟网络”页的屏幕截图，其中选择了“所有网络”选项。](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. 若要将访问范围限制为特定 IP 地址，请确认“所选网络”选项处于选中状态。 在“防火墙”部分中执行以下步骤：
    1. 选择“添加客户端 IP 地址”选项，使当前客户端 IP 可以访问命名空间。 
    2. 对于“地址范围”，请输入某个特定的 IPv4 地址或以 CIDR 表示法表示的 IPv4 地址范围。 

    >[!WARNING]
    > 如果你在此页上选择了“所选网络”选项并且未添加至少一个 IP 防火墙规则或虚拟网络，则可以通过公共 Internet（使用访问密钥）访问该命名空间。
1. 指定是否要“允许受信任的 Microsoft 服务绕过此防火墙”。 有关详细信息，请参阅[受信任的 Microsoft 服务](#trusted-microsoft-services)。 

      ![防火墙 - 选中了“所有网络”选项](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. 在工具栏上选择“保存”，保存这些设置。 请等待几分钟，直到门户通知中显示确认消息。

    > [!NOTE]
    > 若要限制对特定虚拟网络的访问，请参阅[允许从特定网络访问](event-hubs-service-endpoints.md)。

[!INCLUDE [event-hubs-trusted-services](./includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>使用 Resource Manager 模板

> [!IMPORTANT]
> 基本层不支持防火墙功能。

以下资源管理器模板可用于向现有的事件中心命名空间添加 IP 筛选器规则。

模板中的 **ipMask** 是单个 IPv4 地址或者是以 CIDR 表示法表示的一个 IP 地址块。 例如，在 CIDR 表示法中，70.37.104.0/24 表示从 70.37.104.0 到 70.37.104.255 的 256 个 IPv4 地址，其中 24 表示范围的有效前缀位数。

添加虚拟网络或防火墙规则时，请将 `defaultAction` 的值设置为 `Deny`。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
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

若要限制事件中心到 Azure 虚拟网络的访问，请参阅以下链接：

- [事件中心的虚拟网络服务终结点][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
