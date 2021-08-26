---
title: 教程：在 Azure 虚拟 WAN 站点到站点连接上执行数据包捕获
description: 本教程介绍如何在虚拟 WAN 站点到站点 VPN 网关上执行数据包捕获。
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: 765285a8b7c2434c64d1513e510f1cf06b513291
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122598057"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>在 Azure 虚拟 WAN 站点到站点 VPN 网关上执行数据包捕获 

与连接和性能相关的问题通常很复杂。 缩小确定问题原因的范围可能需要花费大量时间和精力。 数据包捕获有助于将问题范围缩小到网络的某些部分。 它有助于确定问题是出现在网络的客户端、网络的 Azure 端还是两者之间的某个位置。 缩小问题范围后，可以更高效地调试和采取补救措施。

以下文章介绍如何在 Azure 虚拟 WAN 站点到站点 VPN 网关上启动和停止数据包捕获。 目前只能通过 PowerShell 使用此功能。

## <a name="prerequisites"></a>先决条件

若要使用本文中的步骤，你必须已在环境中设置以下配置：

* 具有虚拟 WAN、虚拟中心并在虚拟中心部署了站点到站点 VPN 网关。 还可以将 VPN 站点连接到你的站点到站点 VPN 网关。


### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>设置环境

通过在 PowerShell 中运行以下脚本，确保处于正确的订阅上下文。 这可确保登录到有权在站点到站点 VPN 网关上执行数据包捕获的用户。

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> 创建存储帐户

需要在 Azure 订阅下创建一个存储帐户，用于存储数据包捕获的结果。 请参阅此[文档](.././storage/common/storage-account-create.md)了解如何创建存储帐户。

创建帐户后，请运行以下命令，以生成共享访问签名 (SAS) URL。 将通过此 URL 存储数据包捕获的结果。
   ```azurepowershell-interactive
  $rg = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rg -Name $storeName
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>启动数据包捕获

若要启动数据包捕获，有两个选择：在单个 VPN 连接上捕获数据包或在整个站点到站点 VPN 网关上捕获数据包。 请注意，如果选择在 VPN 连接上执行捕获，则每次只能对 6 个连接执行捕获。

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>站点到站点 VPN 网关上的数据包捕获（所有连接）

请运行以下命令。 通过导航到虚拟中心，单击“连接”下的 VPN（站点到站点），可以找到站点到站点 VPN 网关的名称。

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="虚拟 WAN 网关名称的图像。" lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>"
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>特定站点到站点 VPN 连接上的数据包捕获

>[!NOTE]
> 请注意，只能同时在 5 个 VPN 连接上运行数据包捕获。


请运行以下命令。 通过导航到虚拟中心，单击“连接”下的 VPN（站点到站点），可以找到站点到站点 VPN 连接的名称。 然后，导航到要在其上执行数据包捕获的 VPN 站点，然后单击右侧的三个点。 在弹出的菜单中，单击“编辑 VPN 连接”。

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="显示如何查找 VPN 连接名称的图像。" lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

通过单击上一节中的 VPN 站点并查看“链接”表，可以找到特定 VPN 站点的链接的名称。 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="显示如何查找 VPN 链接名称的图像。" lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">”
   ```

## <a name="optional-specifying-filters"></a>可选：指定筛选器

可以使用一些常用数据包捕获工具。 使用这些工具获取相关的数据包捕获会比较繁琐，尤其是在高流量场景中。 若要简化数据包捕获，可以在数据包捕获上指定筛选器，以侧重于特定行为。 下面是可用的参数：

>[!NOTE]
> 对于 TracingFlags 和 TCPFlags，可以指定多个协议，方法是将要捕获的协议的数值相加（与逻辑 OR 相同）。 例如，如果只想捕获 ESP 和 OPVN 数据包，则将 TracingFlag 值指定为 8+1 = 9。  

| 参数 | 说明 | 默认值 | 可用值|
|--- |--- | --- | ---|
| TracingFlags | 用于确定捕获的数据包类型的整数 | 11（ESP、IKE、OVPN） | ESP = 1 IKE  = 2 OPVN = 8 |
| TCPFlags | 用于确定捕获的 TCP 数据包类型的整数 | 0（无） | FIN = 1，SYN = 2，RST = 4，PSH = 8，ACK = 16，URG = 32，ECE = 64，CWR = 128| 
| MaxPacketBufferSize|捕获的数据包的最大大小（字节）。 如果数据包大于提供的值，会将数据包截断。 |120|任意|
| MaxFileSize |最大捕获文件大小（Mb）。 捕获的内容存储在一个循环缓冲区中，以便以先进先出 (FIFO) 方式处理溢出（先删除较旧的数据包）|100|任意|
|SourceSubnets|捕获指定 CIDR 范围的数据包。 指定为数组。|[]（所有 IPv4 地址）|逗号分隔的 IPV4 子网的数组|
| DestinationSubnets |捕获以指定 CIDR 范围为目标的数据包。 指定为数组。 |[]（所有 IPv4 地址）|逗号分隔的 IPV4 子网的数组|
|SourcePort |捕获其源位于指定范围内的数据包。 指定为数组。|[]（所有端口）|逗号分隔的端口的数组|
|DestinationPort |捕获其目标位于指定范围内的数据包。 指定为数组。|[]（所有端口）|逗号分隔的端口的数组|
| CaptureSingleDirectionTrafficOnly |如果为 true，数据包捕获中只会显示双向流的一个方向。 这将捕获 IP 和端口的所有可能组合。|正确|True、False|
|协议|对应于 IANA 协议的整数的数组。 |[]（所有协议）| 在[此处](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)找到的任何协议 |

下面是使用筛选器字符串的数据包捕获的示例。 可以根据自己的需求基于前面的表来更改参数。  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>停止数据包捕获
建议让数据包捕获运行至少 600 秒。 由于路径上多个组件之间的同步问题，较短的数据包捕获可能无法提供完整的数据。 一旦准备好停止数据包捕获，请运行以下命令。

这些参数与“启动数据包捕获”部分中的参数相似。 SAS URL 是在[创建存储帐户](#createstorage)部分中生成的。 如果 `SASurl` 参数配置不正确，则跟踪可能会失败，并显示 Azure 存储错误。

### <a name="gateway-level-packet-capture"></a>网关级数据包捕获

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sasurl
   ```

### <a name="connection-level-packet-captures"></a>连接级数据包捕获

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sasurl
   ```

## <a name="viewing-your-packet-capture"></a>查看数据包捕获

在 Azure 门户中，导航到在“创建存储帐户”中创建的存储帐户，单击容器，然后下载文件。 数据包捕获数据文件以 PCAP 格式生成。 可以使用 Wireshark 或其他常用应用程序打开 PCAP 文件。

## <a name="next-steps"></a>后续步骤

接下来，若要详细了解虚拟 WAN，请参阅：

> [!div class="nextstepaction"]
> * [虚拟 WAN 常见问题解答](virtual-wan-faq.md)
