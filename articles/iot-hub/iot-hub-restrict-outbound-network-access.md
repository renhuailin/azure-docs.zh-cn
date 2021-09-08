---
title: 限制 IoT 中心出站网络访问和数据丢失防护
description: 开发人员指南 - 如何将 IoT 中心配置为仅将数据传出到受信任位置。
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: f4f8044237c82212723a54b677d77bc5aecb95a5
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868484"
---
# <a name="restrict-outbound-network-access-for-azure-iot-hub"></a>限制 Azure IoT 中心的出站网络访问

IoT 中心支持通过[路由到自定义终结点](iot-hub-devguide-messages-d2c.md)、[文件上传](iot-hub-devguide-file-upload.md)和[设备标识导出](iot-hub-bulk-identity-mgmt.md)将数据传出到其他服务。 要在企业环境中进一步提高安全性，请使用 `restrictOutboundNetworkAccess` API 将 IoT 中心限制为仅将数据传出到已明确批准的目标。 目前无法在 Azure 门户中使用此功能。

## <a name="enabling-the-restriction"></a>启用限制

若要启用该功能，请使用任何可更新 IoT 中心资源属性的方法 (`PUT`) 将 `restrictOutboundNetworkAccess` 设置为 `true`，同时添加一个数组形式的、包含完全限定域名 (FQDN) 的 `allowedFqdnList`。 

演示与 [create 或 update 方法](/rest/api/iothub/iothubresource/createorupdate)结合使用的 JSON 表示形式的示例：

```json
{
...
            "properties": {
              ...
                "restrictOutboundNetworkAccess": true,
                "allowedFqdnList": [
                    "my-eventhub.servicebus.windows.net",
                    "iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net"
                ]
              ...
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            }
        }
    }
}
```
若要使用 Azure CLI 进行相同的更新，请运行

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --set properties.restrictOutboundNetworkAccess=true properties.allowedFqdnList="['my-eventhub.servicebus.windows.net','iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net']"
```

## <a name="restricting-outbound-network-access-with-existing-routes"></a>使用现有路由限制出站网络访问

将 `restrictOutboundNetworkAccess` 设置为 `true` 后，尝试向允许的 FQDN 之外的目标发出数据将会失败。 如果不在允许的 FQDN 列表中包含自定义终结点，即使是现有的已配置路由也会停止工作。

## <a name="built-in-endpoint"></a>内置终结点

如果 `restrictOutboundNetworkAccess` 设置为 `true`，则与内置事件中心兼容的终结点无法免除上述限制。 换句话说，必须在允许的 FQDN 列表中包含内置终结点 FQDN，才能让该终结点继续正常工作。

## <a name="next-steps"></a>后续步骤

- 若要使用托管标识来传出数据，请参阅 [IoT 中心对托管标识的支持](iot-hub-managed-identity.md)。
- 若要限制入站网络访问，请参阅[管理 IoT 中心的公用网络访问](iot-hub-public-network-access.md)和 [IoT 中心对使用专用链接的虚拟网络的支持](virtual-network-support.md)。