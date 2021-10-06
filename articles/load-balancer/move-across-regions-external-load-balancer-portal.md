---
title: 使用 Azure 门户将 Azure 外部负载均衡器移到另一个 Azure 区域
description: 使用 Azure 门户通过 Azure 资源管理器模板将外部负载均衡器从一个 Azure 区域移到另一个区域。
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: da37816b68e9984fdf801e3e84d50a57bf290e96
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273545"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>使用 Azure 门户将外部负载均衡器移到另一个区域

在多种情况下，可能需要将某个外部负载均衡器从一个区域移到另一个区域。 例如，你可能想要创建另一个采用相同配置的外部负载均衡器进行测试。 你还可能想要在灾难恢复规划过程中将外部负载均衡器移到另一个区域。

从字面意义讲，不能将 Azure 外部负载均衡器从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板来导出外部负载均衡器的现有配置和公共 IP 地址。 然后，可将资源暂存在另一区域，方法是：将负载均衡器和公共 IP 导出到某个模板，根据目标区域修改参数，然后将该模板部署到新区域。 有关资源管理器和模板的详细信息，请参阅[将资源组导出到模板](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)。


## <a name="prerequisites"></a>先决条件

- 确保 Azure 外部负载均衡器位于要从中移动的 Azure 区域。

- 无法在区域之间移动 Azure 外部负载均衡器。 必须将新的负载均衡器关联到目标区域中的资源。

- 若要导出外部负载均衡器配置并部署模板，以便在另一区域创建外部负载均衡器，需有“网络参与者”角色或更高级别的角色。

- 确定源网络布局和当前正在使用的所有资源。 此布局包括但不限于负载均衡器、网络安全组、公共 IP 和虚拟网络。

- 请验证 Azure 订阅是否允许在目标区域中创建外部负载均衡器。 请联系支持部门，启用所需配额。

- 确保订阅提供足够的资源，以支持添加负载均衡器。 请参阅 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)。

## <a name="prepare-and-move"></a>准备并移动
以下过程说明如何使用资源管理器模板准备好要移动的外部负载均衡器，并使用 Azure 门户将外部负载均衡器配置移到目标区域。 必须先导出外部负载均衡器的公共 IP 配置。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>导出公共 IP 模板并从门户部署公共 IP

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。 
2. 找到包含源公共 IP 的资源组并将其选中。
3. 选择“设置” > “导出模板”。 
4. 选择“导出模板”下的“部署”。
5. 选择“模板” > “编辑参数”，在联机编辑器中打开 parameters.json 文件。
8. 若要编辑公共 IP 名称的参数，请将源公共 IP 名称中 **parameters** 下的 **value** 属性更改为目标公共 IP 的名称。 请将名称括在引号中。

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    在编辑器中选择“保存”。

9.  选择“模板” > “编辑模板”，在联机编辑器中打开 template.json 文件。

10. 若要编辑要将公共 IP 移到的目标区域，请更改 **resources** 下的 **location** 属性：

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```
  
    若要获取区域位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域代码是不带空格的区域名称。 例如，美国中部的代码是 centralus。
    
12. 还可以根据要求和需求更改模板中的其他参数：

    * SKU。 可以在配置中将公共 IP 的 SKU 从 standard 更改为 basic 或者从 basic 更改为 standard，只需在 template.json 文件中更改 **sku** 下的 **name** 属性即可：

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        有关基本和标准 SKU 公共 IP 的区别，请参阅[创建、更改或删除公共 IP 地址](../virtual-network/virtual-network-public-ip-address.md)。

    * **公共 IP 分配方法** 和 **空闲超时**。 可以通过将 **publicIPAllocationMethod** 属性从 **Dynamic** 更改为 **Static** 或者从 **Static** 更改为 **Dynamic**，来更改公共 IP 分配方法。 可以通过将 **idleTimeoutInMinutes** 属性更改为所需值来更改空闲超时值。 默认值为 4。

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        有关分配方法和空闲超时值的信息，请参阅[创建、更改或删除公共 IP 地址](../virtual-network/virtual-network-public-ip-address.md)。

 
13. 在在线编辑器中选择“保存”。

14. 选择“基本信息” > “订阅”，以选择要将目标公共 IP 部署到的订阅。

15. 选择“基本信息” > “资源组”，以选择要将目标公共 IP 部署到的资源组。 可以选择“新建”来为目标公共 IP 创建新的资源组。 请确保所选名称与现有源公共 IP 的源资源组不同。

16. 确认“基本信息” > “位置”是否设置为要将公共 IP 部署到的目标位置。

17. 在“设置”下，确认名称是否与先前在 parameters 编辑器中输入的名称相匹配。

18. 选中“条款和条件”复选框。

19. 选择“购买”以部署目标公共 IP。

20. 如果已有另一个公共 IP 正用于所要移动的负载均衡器的出站 NAT，请重复上述步骤导出第二个出站公共 IP 并将其部署到目标区域。

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>导出外部负载均衡器模板并从 Azure 门户部署负载均衡器

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。 
2. 找到包含源外部负载均衡器的资源组并将其选中。
3. 选择“设置” > “导出模板”。 
4. 选择“导出模板”下的“部署”。
5. 选择“模板” > “编辑参数”，在联机编辑器中打开 parameters.json 文件。

5. 若要编辑外部负载均衡器名称的参数，请将源外部负载均衡器名称的 **value** 属性更改为目标外部负载均衡器的名称。 请将名称括在引号中。

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  若要编辑在前面步骤中移动的目标公共 IP 的值，必须先获取资源 ID，然后将其粘贴到 parameters.json 文件中。 若要获取 ID：

    1. 在另一个浏览器标签页或窗口中，登录到 [Azure 门户](https://portal.azure.com)并选择“资源组”。
    2. 找到包含前面步骤中移动的公共 IP 的目标资源组。 选择该文件夹。
    3. 选择“设置” > “属性”。
    4. 在右侧的边栏选项卡中，突出显示“资源 ID”并将其复制到剪贴板。 或者，可以选择“资源 ID”路径右侧的“复制到剪贴板”。
    5. 将资源 ID 粘贴到在另一个浏览器窗口或标签页中打开的“编辑参数”编辑器中的 **value** 属性：

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. 在在线编辑器中选择“保存”。


7.  如果为该负载均衡器配置了出站 NAT 和出站规则，则此文件中会显示第三个条目，该条目对应于出站公共 IP 的外部 ID。 在 **目标区域** 中重复上述步骤以获取出站公共 IP 的 ID。 将该 ID 粘贴到 parameters.json 文件中：

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  选择“模板” > “编辑模板”，在联机编辑器中打开 template.json 文件。
9.  若要编辑外部负载均衡器配置将要移到的目标区域，请更改 parameters.json 文件中 **resources** 下的 **location** 属性：

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. 若要获取区域位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域代码是不带空格的区域名称。 例如，美国中部的代码是 centralus。

11. 还可以根据要求和需求更改模板中的其他参数：

    * SKU。 可以在配置中将外部负载均衡器的 SKU 从 standard 更改为 basic 或者从 basic 更改为 standard，只需在 template.json 文件中更改 **sku** 下的 **name** 属性即可：

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      有关基本和标准 SKU 负载均衡器之间的区别，请参阅 [Azure 标准负载均衡器概述](./load-balancer-overview.md)。

    * 负载均衡规则。 可以通过在 template.json 文件的 **loadBalancingRules** 节中添加或删除条目，在配置中添加或删除负载均衡规则：

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       有关负载均衡规则的信息，请参阅[什么是 Azure 负载均衡器？](load-balancer-overview.md)。

    * 探测。 可以通过在 template.json 文件的 **probes** 节中添加或删除条目，在配置中添加或删除负载均衡器的探测：

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       有关详细信息，请参阅[负载均衡器运行状况探测](load-balancer-custom-probe-overview.md)。

    * 入站 NAT 规则。 可以通过在 template.json 文件的 **inboundNatRules** 节中添加或删除条目，来添加或删除负载均衡器的入站 NAT 规则：

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        若要完成某个入站 NAT 规则的添加或删除，该规则必须作为 **type** 属性出现在 template.json 文件的末尾，或者已被删除：

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        有关入站 NAT 规则的信息，请参阅[什么是 Azure 负载均衡器？](load-balancer-overview.md)。

    * 出站规则。 可以通过编辑 template.json 文件中的 **outboundRules** 属性，在配置中添加或删除出站规则：

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         有关详细信息，请参阅[负载均衡器出站规则](./load-balancer-outbound-connections.md#outboundrules)。

12. 在在线编辑器中选择“保存”。

13. 选择“基本信息” > “订阅”，以选择要将目标外部负载均衡器部署到的订阅。

15. 选择“基本信息” > “资源组”，以选择要将目标负载均衡器部署到的资源组。 可以选择“新建”来为目标外部负载均衡器创建新的资源组。 或者，可以选择先前为公共 IP 创建的现有资源组。 请确保所选名称与现有源外部负载均衡器的源资源组不同。

16. 确认“基本信息” > “位置”是否设置为要将外部负载均衡器部署到的目标位置。

17. 在“设置”下，确认名称是否与先前在 parameters 编辑器中输入的名称相匹配。 确认是否为配置中的所有公共 IP 填充了资源 ID。

18. 选中“条款和条件”复选框。

19. 选择“购买”以部署目标公共 IP。

## <a name="discard"></a>弃用

若要丢弃目标公共 IP 和外部负载均衡器，请删除它们所在的资源组。 为此，请从门户上的仪表板中选择该资源组，然后选择概述页顶部的“删除”。

## <a name="clean-up"></a>清理

若要提交更改并完成公共 IP 和外部负载均衡器的移动操作，请删除源公共 IP 和外部负载均衡器或资源组。 为此，请从门户上的仪表板中选择该资源组，然后选择每个页面顶部的“删除”。

## <a name="next-steps"></a>后续步骤

在本教程中，你已将一个 Azure 外部负载均衡器从一个区域移到了另一个区域，并清理了源资源。 若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：


- [将资源移到新资源组或订阅中](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [将 Azure VM 移到另一区域](../site-recovery/azure-to-azure-tutorial-migrate.md)