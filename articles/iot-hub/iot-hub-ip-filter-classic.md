---
title: Azure IoT 中心经典 IP 筛选器（已弃用）| Microsoft Docs
description: 如何从经典 IP 筛选器升级以及升级有哪些好处
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 6f326bafb311acedc48c5a349c78f1cd6bcebc87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661148"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>IoT 中心经典 IP 筛选器和升级方式 

用于 IoT 中心的已升级 IP 筛选器可保护内置终结点，且在默认情况下处于安全状态。 虽然我们尽力不进行重大更改，但已升级 IP 筛选器的增强安全模型与经典 IP 筛选器不兼容，因此我们宣布将其停用。 若要了解有关新的已升级 IP 筛选器的详细信息，请参阅[新增功能](#whats-new)和 [IoT 中心 IP 筛选器](iot-hub-ip-filtering.md)。

为了避免服务中断，必须在迁移截止时间之前执行引导式升级，在迁移截止时间将自动执行升级。 若要了解有关迁移时间线的详细信息，请参阅 [Azure 更新](https://aka.ms/ipfilterv2azupdate)。

## <a name="how-to-upgrade"></a>如何升级

1.  访问 Azure 门户
2.  导航到 IoT 中心。
3.  从左侧菜单中选择“网络”。
4.  你应看到一个横幅，提示你将 IP 筛选器升级到新模型。 选择“是”，以继续操作。
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="显示从经典 IP 筛选器升级的横幅提示的图像":::
5.  由于默认情况下新的 IP 筛选器会阻止所有 IP，因此升级会删除你的个人拒绝规则，但你可以在保存之前查看这些规则。 请仔细查看规则，确保它们对你有用。
6.  按照提示完成升级。

## <a name="whats-new"></a>新增功能

### <a name="secure-by-default"></a>默认保护

默认情况下，经典 IP 筛选器会隐式允许所有 IP 地址连接到 IoT 中心，这与最常见的网络安全方案不太一致。 通常，你只希望受信任的 IP 地址能够连接到 IoT 中心并拒绝其他所有内容。 若要使用经典 IP 筛选器实现此目标，需要完成一个多步过程。 例如，如果只想接受来自 `192.168.100.0/22` 的流量，则必须

1. 为 `192.168.100.0/22` 配置单个允许规则。
1. 为 `0.0.0.0/0` 配置不同的阻止规则（“阻止全部”规则）
1. 确保规则排序正确，允许规则位于阻止规则之上。

实际上，此多步过程会造成混淆。 用户未配置“阻止全部”规则，或未对规则进行正确排序，从而导致意外暴露。 

默认情况下，新的 IP 筛选器会阻止所有 IP 地址。 仅允许显式添加的 IP 范围连接到 IoT 中心。 在上面的示例中，不再需要执行步骤 2 和 3。 这一新行为简化了配置，并遵循[默认安全原则](https://wikipedia.org/wiki/Secure_by_default)。

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>保护内置的事件中心兼容终结点

不能将经典 IP 筛选器应用于内置终结点。 此限制意味着，配置了“阻止全部”规则（阻止 `0.0.0.0/0`）的事件仍可从任何 IP 地址访问内置终结点。

新的 IP 筛选器提供了一种将规则应用于内置终结点的选项，该选项可减少暴露于网络安全威胁。

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="显示是否应用于内置终结点的切换的图像":::

> [!NOTE]
> 此选项不能用于免费 (F1) IoT 中心。 若要将 IP 筛选器规则应用于内置终结点，请使用付费 IoT 中心。

### <a name="api-impact"></a>API 影响

从 `2020-08-31`（以及 `2020-08-31-preview`）起，IoT 中心资源 API 中提供已升级 IP 筛选器。 所有 API 版本中仍提供经典 IP 筛选器，但在靠近迁移截止时间的未来 API 版本中，将删除该筛选器。 若要了解有关迁移时间线的详细信息，请参阅 [Azure 更新](https://aka.ms/ipfilterv2azupdate)。

## <a name="tip-try-the-changes-before-they-apply"></a>提示：在应用之前尝试更改

由于默认情况下，新的 IP 筛选器会阻止所有 IP 地址，因此，单独的阻止规则将不再兼容。 因此，引导式升级过程将删除这些单独的阻止规则。 

若要尝试通过经典 IP 筛选器进行更改，请执行以下操作：

1. 访问 IoT 中心的“网络”选项卡
1. 记下现有 IP 筛选器（经典）配置，以防你想要回滚
1. 在包含“阻止”的规则旁边，选择垃圾桶图标以删除这些规则
1. 使用 `0.0.0.0/0` 在底部添加另一规则，然后选择“阻止”
1. 选择“保存”

此配置模拟了从经典升级后新 IP 筛选器的行为。 一个例外情况是内置终结点保护，它无法尝试使用经典 IP 筛选器。 不过，该功能是可选的，因此，如果你认为它可能会中断某些内容，则无需使用它。

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>提示：检查与 IoT 中心的所有 IP 连接的诊断日志

若要确保顺利转换，请在“连接”类别下检查诊断日志。 查找 `maskedIpAddress` 属性以查看这些范围是否符合预期。 请记住：新的 IP 筛选器将阻止未显式添加的所有 IP 地址。

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>IoT 中心经典 IP 筛选器文档（已停用）

> [!IMPORTANT]
> 下面是将停用的经典 IP 筛选器的原始文档。

安全性对于基于 Azure IoT 中心的任何 IoT 解决方案来说都是一个重要方面。 作为安全配置的一部分，有时需要显式指定设备可从其连接的 IP 地址。 使用 *IP 筛选器* 功能，可以配置规则来拒绝或接受来自特定 IPv4 地址的流量。

### <a name="when-to-use"></a>何时使用

对于需要阻止特定 IP 地址的 IoT 中心终结点的情况，有两个具体用例：

* IoT 中心应仅从指定范围内的 IP 地址接收流量并拒绝任何其他流量。 例如，将 IoT 中心与 [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) 配合使用，以在 IoT 中心与本地基础结构之间创建专用连接。

* 需要拒绝来自 IoT 中心管理员已标识为可疑地址的 IP 地址的流量。

### <a name="how-filter-rules-are-applied"></a>筛选器规则的应用方式

在 IoT 中心服务级别应用 IP 筛选器规则。 因此，IP 筛选器规则适用于使用任意受支持协议和从设备和后端应用发出的所有连接。 但是，直接从[与事件中心兼容的内置终结点](iot-hub-devguide-messages-read-builtin.md)（而不是通过 IoT 中心连接字符串）读取数据的客户端不会绑定到 IP 筛选器规则。 

与 IoT 中心的拒绝 IP 规则匹配的 IP 地址发出的任何连接尝试都会收到“未授权”401 状态代码和说明。 响应消息不提及 IP 规则。 拒绝 IP 地址可以阻止其他 Azure 服务（例如 Azure 门户中的 Azure 流分析、Azure 虚拟机或设备资源管理器）与 IoT 中心进行交互。

> [!NOTE]
> 如果必须使用 Azure 流分析 (ASA) 从启用了 IP 筛选器的 IoT 中心读取消息，请使用 IoT 中心的与事件中心兼容的名称和终结点在 ASA 中手动添加[事件中心流输入](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)。

### <a name="default-setting"></a>默认设置

默认情况下，门户中针对 IoT 中心的“IP 筛选器”网格为空。 此默认设置意味着中心会接受来自任何 IP 地址的连接。 此默认设置等效于接受 0.0.0.0/0 IP 地址范围的规则。

若要转到 IP 筛选器设置页，请依次选择“网络”、“公共访问”、“选定的 IP 范围”：  

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="IoT 中心默认 IP 筛选器设置":::

### <a name="add-or-edit-an-ip-filter-rule"></a>添加或编辑 IP 筛选器规则

若要添加 IP 筛选器规则，请选择“+ 添加 IP 筛选器规则”。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="向 IoT 中心添加 IP 筛选器规则":::

选择“添加 IP 筛选器规则”后，填写字段。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="选择“添加 IP 筛选器规则”后":::

* 提供 IP 筛选器规则的 **名称**。 此项必须是不区分大小写的唯一字母数字字符串，长度不超过 128 个字符。 只接受 ASCII 7 位字母数字字符以及以下字符：`{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。

* 提供单个 IPv4 地址或者以 CIDR 表示法提供一个 IP 地址块。 例如，在 CIDR 表示法中，192.168.100.0/22 表示从 192.168.100.0 到 192.168.103.255 的 1024 个 IPv4 地址。

* 选择“允许”或“阻止”作为 IP 筛选器规则的“操作”。   

填写这些字段后，请选择“保存”以保存该规则  。 随后会出现一条警报，告知更新正在进行。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="关于保存 IP 筛选器规则的通知":::

当存在的 IP 筛选规则达到最大数目 10 时，“添加”选项被禁用。

若要编辑现有规则，请选择要更改的数据，进行更改，然后选择“保存”以保存编辑内容。

### <a name="delete-an-ip-filter-rule"></a>删除 IP 筛选器规则

若要删除 IP 筛选器规则，请选择与该行对应的垃圾桶图标，然后选择“保存”。  随即会删除该规则并保存更改。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="删除 IoT 中心 IP 筛选器规则":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>使用 Azure CLI 检索和更新 IP 筛选器

可以通过 [Azure CLI](/cli/azure/) 检索和更新 IoT 中心的 IP 筛选器。

若要检索 IoT 中心的当前 IP 筛选器，请运行：

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

这会返回一个 JSON 对象，其中会在 `properties.ipFilterRules` 键下列出现有 IP 筛选器：

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

若要为 IoT 中心添加新 IP 筛选器，请运行：

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

若要在 IoT 中心删除现有 IP 筛选器，请运行：

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

请注意，`<ipFilterIndexToRemove>` 必须对应于 IoT 中心 `properties.ipFilterRules` 中的 IP 筛选器顺序。

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>使用 Azure PowerShell 检索和更新 IP 筛选器

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以通过 [Azure PowerShell](/powershell/azure/) 检索和设置 IoT 中心的 IP 筛选器。

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

### <a name="update-ip-filter-rules-using-rest"></a>使用 REST 更新 IP 筛选器

还可以使用 Azure 资源提供程序的 REST 终结点检索和修改 IoT 中心的 IP 筛选器。 请参阅 [createorupdate 方法](/rest/api/iothub/iothubresource/createorupdate)`properties.ipFilterRules`。

### <a name="ip-filter-rule-evaluation"></a>IP 筛选器规则评估

IP 筛选器规则按顺序应用，与 IP 地址匹配的第一条规则决定了是采取接受操作还是拒绝操作。

例如，若要接受 192.168.100.0/22 范围内的地址并拒绝所有其他地址，则网格中的第一条规则应接受 192.168.100.0/22 这一地址范围。 下一个规则应通过使用 0.0.0.0/0 范围拒绝所有地址。

可以通过单击行开头的三个竖直点并使用拖放操作更改 IP 筛选规则在网格中的顺序。

若要保存新的 IP 筛选器规则顺序，请单击“保存”。 

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="更改 IoT 中心 IP 筛选规则的顺序":::

## <a name="next-steps"></a>后续步骤

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 IP 筛选器](iot-hub-ip-filtering.md)