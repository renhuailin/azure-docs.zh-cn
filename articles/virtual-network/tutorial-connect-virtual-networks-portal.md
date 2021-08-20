---
title: 使用 VNet 对等互连连接虚拟网络 - 教程 - Azure 门户
description: 本教程介绍如何使用 Azure 门户通过虚拟网络对等互连来连接虚拟网络。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/06/2021
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: ca8829a5f71dabfe33f013c354e083cae08e4566
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113430573"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>教程：通过 Azure 门户使用虚拟网络对等互连连接虚拟网络

可以使用虚拟网络对等互连将虚拟网络互相连接。 这些虚拟网络可以位于相同区域或不同区域中（也称为全局 VNet 对等互连）。 将虚拟网络对等互连后，两个虚拟网络中的资源将能够以相同的延迟和带宽相互通信，就像这些资源位于同一个虚拟网络中一样。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建两个虚拟网络
> * 使用虚拟网络对等互连连接两个虚拟网络。
> * 将虚拟机 (VM) 部署到每个虚拟网络
> * VM 之间进行通信

如果你愿意，可以使用 [Azure CLI](tutorial-connect-virtual-networks-cli.md) 或 [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md) 完成本教程中的步骤。

## <a name="prerequisites"></a>先决条件

在开始之前，需要一个包含有效订阅的 Azure 帐户。 如果没有帐户，可以[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-virtual-networks"></a>创建虚拟网络

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户上，选择“+ 创建资源”。

1. 搜索“虚拟网络”，然后选择“创建” 。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vnet.png" alt-text="为虚拟网络创建资源的屏幕截图。":::

1. 在“基本信息”选项卡上，输入或选择以下信息并接受其余设置的默认值：

    |设置|值|
    |---|---|
    |订阅| 选择订阅。|
    |资源组| 选择“新建”，并输入 myResourceGroup|
    |区域| 选择“美国东部”。|
    |名称|myVirtualNetwork1|

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-basic-tab.png" alt-text="“创建虚拟网络”-“基本信息”选项卡的屏幕截图。":::

1. 在“IP 地址”选项卡上，为“地址空间”字段输入“10.0.0.0/16”。 单击下面的“添加子网”按钮，在“子网名称”中输入“Subnet1”，在“子网地址范围”中输入“10.0.0.0/24”。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/ip-addresses-tab.png" alt-text="“创建虚拟网络”-“IP 地址”选项卡的屏幕截图。":::

1. 选择“查看 + 创建”，然后选择“创建”。
   
1. 再次重复步骤 1-5，使用以下设置创建第二个虚拟网络：

    | 设置 | 值 |
    | --- | --- |
    | 名称 | myVirtualNetwork2 |
    | 地址空间 | 10.1.0.0/16 |
    | 资源组 | 选择“使用现有”，然后选择“myResourceGroup”。|
    | 子网名称 | Subnet2 |
    | 子网地址范围 | 10.1.0.0/24 |

## <a name="peer-virtual-networks"></a>将虚拟网络对等互连

1. 在 Azure 门户顶部的搜索框中，查找“myVirtualNetwork1”。 当“myVirtualNetwork1”出现在搜索结果中时，将其选中。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/search-vnet.png" alt-text="搜索“myVirtualNetwork1”的屏幕截图。":::

1. 在“设置”下选择“对等互连”，然后选择“+ 添加”，如下图所示：  

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-peering.png" alt-text="为 myVirtualNetwork1 创建对等互连的屏幕截图。":::
    
1. 输入或选择以下信息，接受其余设置的默认值，然后选择“添加”。

    | 设置 | 值 |
    | --- | --- |
    | 此虚拟网络 - 对等互连链接名称 | 从 myVirtualNetwork1 到远程虚拟网络的对等互连的名称。  myVirtualNetwork1-myVirtualNetwork2 用于此连接。|
    | 远程虚拟网络 - 对等互连链接名称 |  从远程虚拟网络到 myVirtualNetwork1 的对等互连的名称。 myVirtualNetwork2-myVirtualNetwork1 用于此连接。 |
    | 订阅 | 选择订阅。|
    | 虚拟网络  | 可以在相同区域或不同区域中选择虚拟网络。 从下拉列表中选择“myVirtualNetwork2” |

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png" alt-text="虚拟网络对等互连配置的屏幕截图。" lightbox="./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional-expanded.png":::

    “对等互连状态”为“已连接”，如下图所示：

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png" alt-text="虚拟网络对等互连连接状态的屏幕截图。":::

    如果未看到“已连接”状态，请选择“刷新”按钮。

## <a name="create-virtual-machines"></a>创建虚拟机

在每个虚拟网络中创建一个 VM，以便可以测试虚拟网络之间的通信。

### <a name="create-the-first-vm"></a>创建第一个 VM

1. 在 Azure 门户上，选择“+ 创建资源”。

1. 选择“计算”，然后在“虚拟机”下选择“创建” 。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm.png" alt-text="为虚拟机创建资源的屏幕截图。":::

1. 在“基本信息”选项卡上输入或选择以下信息，接受其余设置的默认值，然后选择“创建” ：

    | 设置 | 值 |
    | --- | --- |
    | 资源组| 选择“使用现有”，然后选择“myResourceGroup”。 |
    | 名称 | myVM1 |
    | 位置 | 选择“美国东部”。 |
    | 映像 | 选择 OS 映像。 对于此 VM，已选择“Windows Server 2019 Datacenter - Gen1”。 |
    | 大小 | 选择 VM 大小。 对于此 VM，已选择“Standard_D2s_v3”。 |
    | 用户名 | 输入用户名。 为此示例选择了用户名“azure”。 |
    | 密码 | 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-)。 |
   
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm-basic-tab.png" alt-text="虚拟机基本信息选项卡配置的屏幕截图。" lightbox="./media/tutorial-connect-virtual-networks-portal/create-vm-basic-tab-expanded.png":::

1. 在“网络”选项卡上，选择以下值：

    | 设置 | 值 |
    | --- | --- |
    | 虚拟网络 | myVirtualNetwork1 - 如果尚未选择它，请选择“虚拟网络”，然后选择“myVirtualNetwork1”。 |
    | 子网 | Subnet1 - 如果尚未选择它，请选择“子网”，然后选择“Subnet1”。 |
    | 公共入站端口 | “允许选定的端口” |
    | 选择入站端口 | *RDP (3389)* |

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm-networking-tab.png" alt-text="虚拟机网络选项卡配置的屏幕截图。" lightbox="./media/tutorial-connect-virtual-networks-portal/create-vm-networking-tab-expanded.png":::

1. 选择“查看 + 创建”，然后选择“创建”开始 VM 部署 。

### <a name="create-the-second-vm"></a>创建第二个 VM

再次重复步骤 1-6，使用以下更改创建第二个虚拟机：

| 设置 | 值 |
| --- | --- |
| 名称 | myVm2 |
| 虚拟网络 | myVirtualNetwork2 |

创建 VM 可能需要数分钟的时间。 在两个 VM 完成创建之前，不要继续执行剩余的步骤。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="communicate-between-vms"></a>VM 之间进行通信

1. 在门户顶部的搜索框中，查找“myVm1”。 当“myVm1”出现在搜索结果中时，请选择它。
    
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/search-vm.png" alt-text="搜索“myVm1”的屏幕截图。":::

1. 若要连接到虚拟机，请选择“连接”，然后从下拉列表中选择“RDP” 。 选择“下载 RDP 文件”以下载远程桌面文件。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png" alt-text="“连接到虚拟机”按钮的屏幕截图。"::: 

1. 若要连接到 VM，请打开已下载的 RDP 文件。 出现提示时，选择“连接”。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/rdp-connect.png" alt-text="远程桌面连接屏幕的屏幕截图。":::

1. 输入在创建 VM 时指定的用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建 VM 时输入的凭据），然后选择“确定”。

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/rdp-credentials.png" alt-text="RDP 凭据屏幕的屏幕截图。":::

1. 你可能会在登录过程中收到证书警告。 选择“是”以继续连接。

1. 在后面的步骤中，将使用 ping 从 myVm2 VM 与 myVm1 VM 进行通信 。 Ping 使用 Internet 控制消息协议 (ICMP)，默认情况下会拒绝 ICMP 通过 Windows 防火墙。 在 *myVm1* VM 上，允许 ICMP 穿过 Windows 防火墙，以便在稍后的步骤中可以使用 PowerShell 从 *myVm2* 对此 VM 执行 ping 命令：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    虽然本教程中使用 ping 在 VM 之间进行通信，但在进行生产部署时，不建议允许 ICMP 通过 Windows 防火墙。

1. 若要连接到 *myVm2* VM，请在 *myVm1* VM 上通过命令提示符输入以下命令：

    ```
    mstsc /v:10.1.0.4
    ```
    
1. 由于启用了对 *myVm1* 的 ping，现在可以按 IP 地址 ping 它：

    ```
    ping 10.0.0.4
    ```
    
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/myvm2-ping-myvm1.png" alt-text="myVM2 ping myVM1 的屏幕截图。":::

1. 断开到 *myVm1* 和 *myVm2* 的 RDP 会话。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除： 

1. 在门户顶部的“搜索”框中输入“myResourceGroup”。 当在搜索结果中看到“myResourceGroup”时，将其选中。

1. 选择“删除资源组”  。

1. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。 

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/delete-resource-group.png" alt-text="“删除资源组”页的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解虚拟网络对等互连](virtual-network-peering-overview.md)
