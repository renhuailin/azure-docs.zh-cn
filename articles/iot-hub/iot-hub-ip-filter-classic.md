---
title: Azure IoT 中心经典 IP 筛选器 (弃用) |Microsoft Docs
description: 如何从经典 IP 筛选器升级以及有哪些优点
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 70cea7a388c07bee9caa2e25e4061a3d3bb2b460
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633822"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>IoT 中心经典 IP 筛选器和升级方式 

用于 IoT 中心的升级 IP 筛选器保护内置终结点，默认情况下是安全的。 虽然我们不会进行重大更改，但升级后的 IP 筛选器的增强安全模型与经典 IP 筛选器不兼容，因此我们宣布其停用。 若要了解有关新的已升级 IP 筛选器的详细信息，请参阅新增 [功能](#whats-new) 和 [IoT 中心 IP 筛选器](iot-hub-ip-filtering.md)。

若要避免服务中断，必须在迁移截止时间之前执行引导式升级，此时将自动执行升级。 若要了解有关迁移时间线的详细信息，请参阅 [Azure update](https://aka.ms/ipfilterv2azupdate)。

## <a name="how-to-upgrade"></a>如何升级

1.  访问 Azure 门户
2.  导航到 IoT 中心。
3.  从左侧菜单中选择“网络”。
4.  你会看到一个横幅，提示你将 IP 筛选器升级到新模型。  选择“是”继续。
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="显示从经典 IP 筛选器升级的横幅提示的图像":::
5.  由于默认情况下新的 IP 筛选器会阻止所有 IP，升级会删除你的单个拒绝规则，但你可以在保存之前查看它们。 请仔细查看规则，以确保它们适用于你。
6.  按照提示完成升级。

## <a name="whats-new"></a>新变化

### <a name="secure-by-default"></a>默认保护

默认情况下，典型 IP 筛选器会隐式允许所有 IP 地址连接到 IoT 中心，这与最常见的网络安全方案不一致。 通常，你只希望受信任的 IP 地址能够连接到 IoT 中心并拒绝其他所有内容。 为实现此目标和经典 IP 筛选器，这是一个多步骤过程。 例如，如果只想接受来自的流量 `192.168.100.0/22` ，则必须

1. 为配置单个 *允许* 规则 `192.168.100.0/22` 。
1. 为 `0.0.0.0/0` ("阻止全部" 规则配置不同的块规则) 
1. 请确保规则排序正确，并在阻止规则之上排序 allow 规则。

实际上，这一多步骤过程会导致混淆。 用户未配置 "阻止全部" 规则，或未正确对规则进行排序，导致意外泄露。 

默认情况下，新的 IP 筛选器会阻止所有 IP 地址。 仅允许显式添加的 IP 范围连接到 IoT 中心。 在上面的示例中，不再需要执行步骤2和步骤3。 这一新行为通过 [安全的默认原则](https://wikipedia.org/wiki/Secure_by_default)简化了配置和遵守美国。

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>保护内置的与事件中心兼容的终结点

不能将经典 IP 筛选器应用到内置终结点。 此限制意味着，具有阻止所有规则 (块 `0.0.0.0/0`) 配置的事件，仍可从任何 IP 地址访问内置终结点。

新的 IP 筛选器提供了一种将规则应用到内置终结点的选项，从而减少了网络安全威胁的风险。

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="显示要应用于内置终结点的切换的图像":::

> [!NOTE]
> 此选项不能用于免费 (F1) IoT 中心。 若要将 IP 筛选规则应用到内置终结点，请使用付费 IoT 中心。

### <a name="api-impact"></a>API 影响

升级的 IP 筛选器在 IoT 中心资源 API 中提供，从 (，以及 `2020-08-31` `2020-08-31-preview`) 和更高版本。 经典 IP 筛选器在所有 API 版本中仍可用，但将在未来的 API 版本中删除，截止时间晚于迁移截止时间。 若要了解有关迁移时间线的详细信息，请参阅 [Azure update](https://aka.ms/ipfilterv2azupdate)。

## <a name="tip-try-the-changes-before-they-apply"></a>提示：在应用之前尝试更改

由于默认情况下，新的 IP 筛选器会阻止所有 IP 地址，因此，单个块规则不再兼容。 因此，引导式升级过程将删除这些单独的块规则。 

若要尝试通过经典 IP 筛选器进行更改，请执行以下操作：

1. 访问 IoT 中心的 " **网络** " 选项卡
1. 请注意 (经典) 配置的现有 IP 筛选器，以防你想要回滚
1. 在 "带 **块** 的规则" 旁边，选择 "垃圾桶" 图标以删除它们
1. 在底部添加另一个规则 `0.0.0.0/0` ，然后选择 "**阻止**"
1. 选择“保存”

此配置模拟了从经典升级后新 IP 筛选器的行为方式。 一个例外是内置的 endpoint protection，无法尝试使用经典 IP 筛选器。 不过，该功能是可选的，因此，如果您认为它可能会中断某些内容，则无需使用此功能。

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>提示：检查到 IoT 中心的所有 IP 连接的诊断日志

若要确保顺利转换，请在 "连接" 类别下检查诊断日志。 查找 `maskedIpAddress` 属性以查看这些范围是否与预期的相同。 请记住：新的 IP 筛选器将阻止所有未显式添加的 IP 地址。

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>IoT 中心经典 IP 筛选器文档 (退休) 

> [!IMPORTANT]
> 下面是要停用的经典 IP 筛选器的原始文档。

安全性对于基于 Azure IoT 中心的任何 IoT 解决方案来说都是一个重要方面。 作为安全配置的一部分，有时需要显式指定设备可从其连接的 IP 地址。 使用 *IP 筛选器* 功能，可以配置规则来拒绝或接受来自特定 IPv4 地址的流量。

### <a name="when-to-use"></a>何时使用

对于需要阻止特定 IP 地址的 IoT 中心终结点的情况，有两个具体用例：

* IoT 中心应仅从指定范围内的 IP 地址接收流量并拒绝任何其他流量。 例如，将 IoT 中心与 [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) 配合使用，以在 IoT 中心与本地基础结构之间创建专用连接。

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

可以通过 [Azure CLI](https://docs.microsoft.com/cli/azure/) 检索和更新 IoT 中心的 IP 筛选器。

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

还可以使用 Azure 资源提供程序的 REST 终结点检索和修改 IoT 中心的 IP 筛选器。 请参阅 [createorupdate 方法](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate)`properties.ipFilterRules`。

### <a name="ip-filter-rule-evaluation"></a>IP 筛选器规则评估

IP 筛选器规则按顺序应用，与 IP 地址匹配的第一条规则决定了是采取接受操作还是拒绝操作。

例如，若要接受 192.168.100.0/22 范围内的地址并拒绝所有其他地址，则网格中的第一条规则应接受 192.168.100.0/22 这一地址范围。 下一个规则应通过使用 0.0.0.0/0 范围拒绝所有地址。

可以通过单击行开头的三个竖直点并使用拖放操作更改 IP 筛选规则在网格中的顺序。

若要保存新的 IP 筛选器规则顺序，请单击“保存”。 

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="更改 IoT 中心 IP 筛选规则的顺序":::

## <a name="next-steps"></a>后续步骤

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 IP 筛选器](iot-hub-ip-filtering.md)