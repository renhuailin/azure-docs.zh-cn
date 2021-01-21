---
title: Azure IoT 中心 IP 筛选器 | Microsoft Docs
description: 如何使用 IP 筛选允许从特定 IP 地址连接到 Azure IoT 中心。
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: jlian
ms.openlocfilehash: c6544e8ac00744602476207a89567aea5afe5b1d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632342"
---
# <a name="use-ip-filters"></a>使用 IP 筛选器

安全性对于基于 Azure IoT 中心的任何 IoT 解决方案来说都是一个重要方面。 作为安全配置的一部分，有时需要显式指定设备可从其连接的 IP 地址。 使用 *IP 筛选器* 功能，可以配置规则来拒绝或接受来自特定 IPv4 地址的流量。

## <a name="when-to-use"></a>何时使用

使用 IP 筛选器仅接收来自指定范围的 IP 地址的流量，并拒绝其他所有内容。 例如，将 IoT 中心与 [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) 一起使用，以在 IoT 中心与本地基础结构之间创建专用连接。

## <a name="default-setting"></a>默认设置

若要转到 IP 筛选器设置页，请依次选择“网络”、“公共访问”、“选定的 IP 范围”：  

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="IoT 中心默认 IP 筛选器设置":::

默认情况下，门户中针对 IoT 中心的“IP 筛选器”网格为空。 此默认设置意味着集线器阻止来自所有 IP 地址的连接。 此默认设置等效于阻止 `0.0.0.0/0` IP 地址范围的规则。

## <a name="add-or-edit-an-ip-filter-rule"></a>添加或编辑 IP 筛选器规则

若要添加 IP 筛选器规则，请选择“+ 添加 IP 筛选器规则”。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="向 IoT 中心添加 IP 筛选器规则":::

选择“添加 IP 筛选器规则”后，填写字段。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="选择“添加 IP 筛选器规则”后":::

* 提供 IP 筛选器规则的 **名称**。 此名称必须是唯一的、不区分大小写的字母数字字符串，最长为128个字符。 只接受 ASCII 7 位字母数字字符以及以下字符：`{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。

* 提供单个 IPv4 地址或者以 CIDR 表示法提供一个 IP 地址块。 例如，在 CIDR 表示法中，192.168.100.0/22 表示从 192.168.100.0 到 192.168.103.255 的 1024 个 IPv4 地址。

填写这些字段后，请选择“保存”以保存该规则  。 随后会出现一条警报，告知更新正在进行。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="关于保存 IP 筛选器规则的通知":::

当存在的 IP 筛选规则达到最大数目 10 时，“添加”选项被禁用。

若要编辑现有规则，请选择要更改的数据，进行更改，然后选择“保存”以保存编辑内容。

## <a name="delete-an-ip-filter-rule"></a>删除 IP 筛选器规则

若要删除 IP 筛选器规则，请选择与该行对应的垃圾桶图标，然后选择“保存”。  随即会删除该规则并保存更改。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="删除 IoT 中心 IP 筛选器规则":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>将 IP 筛选规则应用到内置的与事件中心兼容的终结点

若要将 IP 筛选规则应用到内置的与事件中心兼容的终结点，请选中 " **将 ip 筛选器应用到内置终结点**" 旁边的框，然后选择 " **保存**"。

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="显示内置终结点的切换并保存的图像":::

> [!NOTE]
> 此选项不能用于免费 (F1) IoT 中心。 若要将 IP 筛选规则应用到内置终结点，请使用付费 IoT 中心。

启用此选项后，IP 筛选器规则会复制到内置终结点，因此只有受信任的 IP 范围可以访问它。

如果禁用此选项，则所有 IP 地址都可访问内置终结点。 如果要从包含服务的终结点读取 Azure 流分析等 IP 地址，则此行为可能会很有用。 

## <a name="how-filter-rules-are-applied"></a>筛选器规则的应用方式

在 IoT 中心服务级别应用 IP 筛选器规则。 因此，IP 筛选器规则适用于使用任意受支持协议和从设备和后端应用发出的所有连接。 此外，还可以选择内置的 [与事件中心兼容的终结点](iot-hub-devguide-messages-read-builtin.md) (不通过 IoT 中心连接字符串) 绑定到这些规则。 

任何来自未明确允许的 IP 地址的连接尝试都会收到未经授权的401状态代码和说明。 响应消息不提及 IP 规则。 拒绝 IP 地址可以阻止其他 Azure 服务（例如 Azure 门户中的 Azure 流分析、Azure 虚拟机或设备资源管理器）与 IoT 中心进行交互。

> [!NOTE]
> 如果必须使用 Azure 流分析 (ASA) 从启用了 IP 筛选器的 IoT 中心读取消息，请 **禁用** " **将 Ip 筛选器应用到内置终结点** " 选项，然后使用 IoT 中心的事件中心兼容的名称和终结点在 ASA 中手动添加 [事件中心流输入](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) 。

### <a name="ordering"></a>中间件排序

IP 筛选器规则 *允许* 规则，而无需排序即可应用。 仅允许你添加的 IP 地址连接到 IoT 中心。 

例如，如果要接受范围中的地址 `192.168.100.0/22` 并拒绝其他所有内容，则只需在具有地址范围的网格中添加一条规则 `192.168.100.0/22` 。

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>使用 Azure CLI 检索和更新 IP 筛选器

可以通过 [Azure CLI](/cli/azure/) 检索和更新 IoT 中心的 IP 筛选器。

若要检索 IoT 中心的当前 IP 筛选器，请运行：

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

这会返回一个 JSON 对象，其中会在 `properties.networkRuleSets` 键下列出现有 IP 筛选器：

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

若要为 IoT 中心添加新 IP 筛选器，请运行：

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

若要在 IoT 中心删除现有 IP 筛选器，请运行：

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

此处， `<ipFilterIndexToRemove>` 必须对应于 IoT 中心的 IP 筛选器顺序 `properties.networkRuleSets.ipRules` 。

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>使用 Azure PowerShell 检索和更新 IP 筛选器

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以通过 [Azure PowerShell](/powershell/azure/) 检索和设置 IoT 中心的 IP 筛选器。

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>使用 REST 更新 IP 筛选器


还可以使用 Azure 资源提供程序的 REST 终结点检索和修改 IoT 中心的 IP 筛选器。 请参阅 [createorupdate 方法](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate)`properties.networkRuleSets`。

## <a name="ip-filter-classic-retirement"></a>IP 筛选器 (经典) 停用

经典 IP 筛选器已停用。 若要了解详细信息，请参阅 [IoT 中心经典 IP 筛选器和升级方式](iot-hub-ip-filter-classic.md)。

## <a name="next-steps"></a>后续步骤

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心指标](iot-hub-metrics.md)
* [IoT 中心支持具有专用链接和托管标识的虚拟网络](virtual-network-support.md)
* [管理 IoT 中心的公用网络访问](iot-hub-public-network-access.md)
* [监视 IoT 中心](monitor-iot-hub.md)
