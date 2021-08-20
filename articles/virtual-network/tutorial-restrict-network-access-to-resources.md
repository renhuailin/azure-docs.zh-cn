---
title: 限制对 PaaS 资源的访问 - 教程 - Azure 门户
description: 本教程介绍如何使用 Azure 门户通过虚拟网络服务终结点限制对 Azure 资源（例如 Azure 存储）的网络访问。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/16/2021
ms.author: kumud
ms.openlocfilehash: a0d721e847d0e47358bfbeb640b9e9a6e6a551a3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463357"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>教程：使用 Azure 门户通过虚拟网络服务终结点限制对 PaaS 资源的网络访问

通过虚拟网络服务终结点，可将某些 Azure 服务资源限制为仅允许某个虚拟网络子网通过网络进行访问。 还可以删除对资源的 Internet 访问。 服务终结点提供从虚拟网络到受支持 Azure 服务的直接连接，使你能够使用虚拟网络的专用地址空间访问 Azure 服务。 通过服务终结点发往 Azure 资源的流量始终保留在 Microsoft Azure 主干网络上。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建包含一个子网的虚拟网络
> * 添加子网并启用服务终结点
> * 创建 Azure 资源并且仅允许从一个子网对其进行网络访问
> * 将虚拟机 (VM) 部署到每个子网
> * 确认从某个子网对资源的访问
> * 确认已拒绝从某个子网和 Internet 来访问资源

如果你愿意，可以使用 [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) 或 [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md) 完成本教程中的步骤。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择 Azure 门户左上角的“+ 创建资源”。 搜索“虚拟网络”，然后选择“创建” 。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-resources.png" alt-text="在“创建资源”页中搜索“虚拟网络”时的屏幕截图。":::    

1. 在“基本信息”选项卡上输入以下信息，然后选择“下一步: IP 地址 >” 。 

   | 设置 | 值 |
   |----|----|
   | 订阅 | 选择订阅|
   | 资源组 | 选择“新建”，并输入 myResourceGroup|
   | 名称 | 输入“myVirtualNetwork” |
   | 区域 | 选择“(US)美国东部” |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png" alt-text="“创建虚拟网络”的“基本信息”选项卡的屏幕截图。":::  

1. 在“IP 地址”选项卡上选择以下 IP 地址设置，然后选择“查看 + 创建” 。
   
   | 设置 | 值 |
   | --- | --- |
   | IPv4 地址空间| 保留为默认值。 |
   | 子网名称 | 选择“默认”并将子网名称更改为“公共”。 |
   | 子网地址范围 | 保留为默认值。 |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-virtual-network-ip-addresses.png" alt-text="“创建虚拟网络”的“IP 地址”选项卡的屏幕截图。":::  

1. 如果通过了验证检查，请选择“创建”。

1. 等待部署完成，然后选择“转到资源”或转到下一部分。 

## <a name="enable-a-service-endpoint"></a>启用服务终结点

每个服务、每个子网均启用服务终结点。 创建子网并为该子网启用服务终结点：

1. 如果你尚未进入虚拟网络资源页，可在门户顶部的框中搜索新建的网络。 输入“myVirtualNetwork”并从列表中选择它。

1. 在“设置”下选择“子网”，然后选择“+ 子网”，如下所示：

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-subnet.png" alt-text="将子网添加到现有虚拟网络的屏幕截图。":::

1. 在“添加子网”页上选择或输入以下信息，然后选择“保存” ：

    | 设置 |值 |
    | --- | --- |
    | 名称 | 专用 |
    | 子网地址范围 | 保留为默认值|
    | 服务终结点 | 选择“Microsoft.Storage”|
    | 服务终结点策略 | 保留默认值。 已选择 0 个。 |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-subnet-settings.png" alt-text="“添加子网”页的屏幕截图，其中已配置服务终结点。":::  

> [!CAUTION]
> 在为其中有资源的现有子网启用服务终结点之前，请参阅[更改子网设置](virtual-network-manage-subnet.md#change-subnet-settings)。

## <a name="restrict-network-access-for-a-subnet"></a>限制子网的网络访问

默认情况下，子网中的所有虚拟机实例均可与任何资源通信。 可以通过创建网络安全组并将其关联到子网来限制与子网中所有资源的通信：

1. 在 Azure 门户顶部的搜索框中，搜索“网络安全组”。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/search-network-security-groups.png" alt-text="搜索“网络安全组”时的屏幕截图。":::  

1. 在“网络安全组”页上，选择“+ 创建”。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/network-security-groups-page.png" alt-text="网络安全组登陆页的屏幕截图。"::: 

1. 输入或选择以下信息：

    |设置|值|
    |----|----|
    |订阅| 选择订阅|
    |资源组 | 从列表中选择“myResourceGroup”|
    |名称| 输入“myNsgPrivate” |
    |位置| 选择“美国东部” |

1. 选择“查看 + 创建”，并在通过了验证检查后选择“创建” 。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-nsg-page.png" alt-text="“创建网络安全组”页的屏幕截图。":::

1. 创建网络安全组后，选择“转到资源”，或者在 Azure 门户顶部搜索“myNsgPrivate”。

1. 在“设置”下选择“出站安全规则”，然后选择“+ 添加”。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-rule.png" alt-text="添加出站安全规则时的屏幕截图。" lightbox="./media/tutorial-restrict-network-access-to-resources/create-outbound-rule-expanded.png":::

1. 创建一条允许出站通信到 Azure 存储服务的规则。 输入或选择以下信息，然后选择“添加”：

    |设置|值|
    |----|----|
    |Source| 选择“VirtualNetwork” |
    |源端口范围| * |
    |目标 | 选择“服务标记”|
    |目标服务标记 | 选择“存储”|
    |服务 | 保留默认值“自定义”。 |
    |目标端口范围| 更改为 445。 SMB 协议用于连接到在后续步骤中创建的文件共享。 |
    |协议|任意|
    |操作|Allow|
    |优先度|100|
    |名称|重命名为 Allow-Storage-All|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-storage-rule.png" alt-text="创建用于访问存储的出站安全规则时的屏幕截图。":::

1. 创建另一条出站安全规则，拒绝到 Internet 的通信。 此规则将覆盖所有网络安全组中允许出站 Internet 通信的默认规则。 使用以下值完成上述步骤 6-9，然后选择“添加”：

    |设置|值|
    |----|----|
    |Source| 选择“VirtualNetwork” |
    |源端口范围| * |
    |目标 | 选择“服务标记”|
    |目标服务标记| 选择“Internet”|
    |服务| 保留默认值“自定义”。 |
    |目标端口范围| * |
    |协议|任意|
    |操作| 将默认值更改为“拒绝”。 |
    |优先级|110|
    |名称|更改为 Deny-Internet-All|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-internet-rule.png" alt-text="创建用于阻止 Internet 访问的出站安全规则时的屏幕截图。":::

1. 创建一个允许从任何位置向该子网发送远程桌面协议 (RDP) 流量的入站安全规则。 该规则将替代拒绝来自 Internet 的所有入站流量的默认安全规则。 允许与子网建立远程桌面连接，以便可以在后续步骤中测试连接。 在“设置”下选择“入站安全规则”，然后选择“+ 添加”。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-inbound-rule.png" alt-text="添加入站安全规则时的屏幕截图。" lightbox="./media/tutorial-restrict-network-access-to-resources/create-inbound-rule-expanded.png":::

1. 输入或选择以下值，然后选择“添加”。

    |设置|值|
    |----|----|
    |源| 任意 |
    |源端口范围| * |
    |目标 | 选择“VirtualNetwork”|
    |目标端口范围| 更改为 3389 |
    |协议|任意|
    |操作|Allow|
    |优先级|120|
    |名称|更改为 Allow-RDP-All|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-inbound-rdp-rule.png" alt-text="创建允许入站远程桌面规则时的屏幕截图。":::

   >[!WARNING] 
   > RDP 端口 3389 公开给 Internet。 建议仅用于测试。 对于生产环境，建议使用 VPN 或专用连接。

1.  在“设置”下选择“子网”，然后选择“+ 关联”。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/associate-subnets-page.png" alt-text="网络安全组子网关联页的屏幕截图。":::

1.  在“虚拟网络”下选择“myVirtualNetwork”，然后在“子网”下选择“专用”。 选择“确定”将网络安全组关联到所选的子网。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/associate-private-subnet.png" alt-text="将网络安全组关联到专用子网时的屏幕截图。":::

## <a name="restrict-network-access-to-a-resource"></a>限制对资源的网络访问

对于通过为服务终结点启用的 Azure 服务创建的资源，限制对其的网络访问时所需的步骤因服务而异。 请参阅各个服务的文档来了解适用于每个服务的具体步骤。 本教程的余下内容以示例的形式介绍了限制 Azure 存储帐户进行网络访问的步骤。

### <a name="create-a-storage-account"></a>创建存储帐户

1. 选择 Azure 门户左上角的“+ 创建资源”。

1. 在搜索栏中输入“存储帐户”，然后从下拉菜单中选择它。 然后选择“创建”。

1. 输入以下信息：

    |设置|值|
    |----|----|
    |订阅| 选择订阅|
    |资源组| 选择“myResourceGroup”|
    |存储帐户名称| 输入在所有 Azure 位置均唯一的名称。 该名称的长度必须为 3-24 个字符，并且该名称只能使用数字和小写字母。|
    |区域| 选择“(US)美国东部” |
    |性能|标准|
    |冗余| 本地冗余存储 (LRS)|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-storage-account.png" alt-text="创建新存储帐户时的屏幕截图。":::

1. 选择“创建 + 查看”，并在通过了验证检查后选择“创建” 。 

    >[!NOTE] 
    > 部署可能需要几分钟时间完成。

1. 创建存储帐户后，选择“转到资源”。

### <a name="create-a-file-share-in-the-storage-account"></a>在存储帐户中创建文件共享

1. 在“数据存储”下选择“文件共享”，然后选择“+ 文件共享”。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/file-share-page.png" alt-text="存储帐户中“文件共享”页的屏幕截图。":::

1. 为文件共享输入或设置以下值，然后选择“创建”：

    |设置|值|
    |----|----|
    |名称| my-file-share|
    |Quota| 选择“设置为最大值”。 |
    |层| 保留默认值“事务优化”。 |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-new-file-share.png" alt-text="新建文件共享设置页的屏幕截图。":::

1. 如果未在页面顶部选择“刷新”按钮，则新文件共享应会显示在文件共享页上。

### <a name="restrict-network-access-to-a-subnet"></a>限制对子网的网络访问

默认情况下，存储帐户接受来自任何网络（包括 Internet）中的客户端的网络连接。 可限制来自 Internet 以及所有虚拟网络中的所有其他子网的网络访问（除 myVirtualNetwork 虚拟网络中的“专用”子网外） 。限制对子网的网络访问：

1. 在你的（唯一命名）存储帐户的“设置”下选择“网络”。

1. 选择“允许从选定的网络访问”，然后选择“+ 添加现有虚拟网络”。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/storage-network-settings.png" alt-text="存储帐户网络设置页的屏幕截图。":::

1. 在“添加网络”下选择以下值，然后选择“添加”： 

    |设置|值|
    |----|----|
    |订阅| 选择订阅|
    |虚拟网络| **myVirtualNetwork**|
    |子网| **专用**|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-virtual-network.png" alt-text="将虚拟网络添加到存储帐户页时的屏幕截图。":::

1. 选择“保存”按钮保存虚拟网络配置。

1. 在存储帐户的“安全性 + 网络”下选择“访问密钥”，然后选择“显示密钥”。 记下 key1 的值，以便稍后在 VM 中映射文件共享时使用。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/storage-access-key.png" alt-text="存储帐户密钥和连接字符串的屏幕截图。" lightbox="./media/tutorial-restrict-network-access-to-resources/storage-access-key-expanded.png":::

## <a name="create-virtual-machines"></a>创建虚拟机

若要测试对存储帐户的网络访问，请向每个子网部署 VM。

### <a name="create-the-first-virtual-machine"></a>创建第一个虚拟机

1. 在 Azure 门户上，选择“+ 创建资源”。

1. 选择“计算”，然后在“虚拟机”下选择“创建” 。

1. 在“基本信息”选项卡上，输入或选择以下信息：

   |设置|值|
   |----|----|
   |订阅| 选择订阅|
   |资源组| 选择前面创建的“myResourceGroup”。|
   |虚拟机名称| 输入“myVmPublic”|
   |区域 | （美国）美国东部
   |可用性选项| 可用性区域|
   |可用性区域 | 1 |
   |图像 | 选择 OS 映像。 对于此 VM，已选择“Windows Server 2019 Datacenter - Gen1”。 |
   |大小 | 选择要使用的 VM 实例大小 |
   |用户名|输入所选用户名。|
   |密码| 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-)。|
   |公共入站端口 | 允许所选端口 |
   |选择入站端口 | 保留默认设置为“RDP (3389)” |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-public-vm-settings.png" alt-text="“创建公共虚拟机”设置的屏幕截图。" lightbox="./media/tutorial-restrict-network-access-to-resources/create-public-vm-settings-expanded.png":::
  
1. 在“网络”选项卡上，输入或选择以下信息：

    |设置|值|
    |----|----|
    | 虚拟网络 | 选择“myVirtualNetwork”。 |
    | 子网 | 选择“公共”。 |
    | NIC 网络安全组 | 选择“高级”。 门户会自动为你创建一个允许端口 3389 的网络安全组。 需将此端口保持打开状态，以便在后续步骤中能够连接到虚拟机。 |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/virtual-machine-networking.png" alt-text="“创建公共虚拟机网络”设置的屏幕截图。" lightbox="./media/tutorial-restrict-network-access-to-resources/virtual-machine-networking-expanded.png":::

1. 依次选择“查看并创建”、“创建”，然后等待部署完成 。

1. 选择“转到资源”，或打开“主页”>“虚拟机”页并选择刚刚创建的 VM“myVmPublic”，这应会启动该 VM 。

### <a name="create-the-second-virtual-machine"></a>创建第二个虚拟机

1. 重复步骤 1-5 创建第二个虚拟机。 在步骤 3 中，请将虚拟机命名为 myVmPrivate，并将“NIC 网络安全组”设置为“无” 。 在步骤 4 中，选择“专用”子网。

   :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/virtual-machine-2-networking.png" alt-text="“创建专用虚拟机网络”设置的屏幕截图。" lightbox="./media/tutorial-restrict-network-access-to-resources/virtual-machine-2-networking-expanded.png":::

1. 依次选择“查看并创建”、“创建”，然后等待部署完成 。 

    > [!WARNING]
    > 在部署完成之前，请不要继续执行下一步。

1. 选择“转到资源”，或打开“主页”>“虚拟机”页并选择刚刚创建的 VM“myVmPrivate”，这应会启动该 VM 。

## <a name="confirm-access-to-storage-account"></a>确认对存储帐户的访问

1. 创建 myVmPrivate VM 后，转到该虚拟机的概述页。 选择“连接”按钮连接到该 VM，然后从下拉列表中选择“RDP” 。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/connect-private-vm.png" alt-text="用于专用虚拟机的“连接”按钮的屏幕截图。":::

1. 选择“下载 RDP 文件”将远程桌面文件下载到计算机。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/download-rdp-file.png" alt-text="下载专用虚拟机 RDP 文件时的屏幕截图。":::
  
1. 打开下载的 rdp 文件。 出现提示时，选择“连接”。 

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/rdp-connect.png" alt-text="专用虚拟机的“连接”屏幕的屏幕截图。":::

1. 输入在创建 VM 时指定的用户名和密码。 可能需要选择“更多选择”，然后选择“使用其他帐户”，以指定在创建 VM 时输入的凭据 。 对于电子邮件字段，请输入前面指定的“管理员帐户: 用户名”凭据。 选择“确定”以登录到 VM。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/credential-screen.png" alt-text="专用虚拟机凭据屏幕的屏幕截图。":::

    > [!NOTE] 
    > 你可能会在登录过程中收到证书警告。 如果收到警告，请选择“是”或“继续”以继续连接。  

1. 登录后，打开 Windows PowerShell。 使用下面的脚本，通过 PowerShell 将 Azure 文件共享映射到驱动器 Z。 请将 `<storage-account-key>` 和 `<storage-account-name>` 变量替换为前面在[创建存储帐户](#create-a-storage-account)步骤中提供的并已记下的值。

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell 将返回类似于以下示例的输出：

   ```powershell
   Name        Used (GB)     Free (GB) Provider      Root
   ----        ---------     --------- --------      ----
   Z                                      FileSystem    \\mystorage007.file.core.windows.net\my-f...
   ```

   Azure 文件共享已成功映射到驱动器 Z。

1.   关闭与 *myVmPrivate* VM 建立的远程桌面会话。

## <a name="confirm-access-is-denied-to-storage-account"></a>确认已拒绝对存储帐户的访问

### <a name="from-myvmpublic"></a>在 myVmPublic 中：

1. 在门户顶部的“搜索资源、服务和文档”框中，输入 *myVmPublic*。 当“myVmPublic”出现在搜索结果中时，将其选中。

1. 针对 myVmPublic VM 重复前面在[确认对存储帐户的访问权限](#confirm-access-to-storage-account)中执行的步骤 1-5。

   稍等片刻，你会收到 `New-PSDrive : Access is denied` 错误。 访问被拒绝，因为 *myVmPublic* VM 部署在“公共”子网中。 “公共”子网不包含为 Azure 存储启用的服务终结点。 存储帐户仅允许从“专用”子网访问网络，而不允许从“公共”子网访问。

    ```powershell
    New-PSDrive : Access is denied
    At line:1 char:1
    + New-PSDrive -Name Z -PSProvider FileSystem -Root "\\mystorage007.file ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : InvalidOperation: (Z:PSDriveInfo) [New-PSDrive],     Win32Exception
        + Fu llyQualifiedErrorId : CouldNotMapNetworkDrive,Microsoft.PowerShell.Commands.NewPSDriveCommand

    ```

4. 关闭与 *myVmPublic* VM 建立的远程桌面会话。

### <a name="from-a-local-machine"></a>在本地计算机中：

1. 在 Azure 门户中，转到前面创建的唯一命名的存储帐户。 例如 mystorage007。

1. 在“数据存储”下选择“文件共享”，然后选择前面创建的“my-file-share” 。

1. 会收到以下错误信息：

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/access-denied-error.png" alt-text="“拒绝访问”错误消息的屏幕截图。":::

>[!NOTE] 
> 访问被拒绝，因为计算机不在 MyVirtualNetwork 虚拟网络的“专用”子网中 。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除：

1. 在门户顶部的“搜索”框中输入“myResourceGroup”。 当在搜索结果中看到“myResourceGroup”时，将其选中。

1. 选择“删除资源组”  。

1. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本教程中，我们为虚拟网络子网启用了服务终结点。 我们已了解，可为通过多个 Azure 服务部署的资源启用服务终结点。 你已创建一个 Azure 存储帐户，并已将对该存储帐户的网络访问限制为只能从某个虚拟网络子网中的资源进行。 若要详细了解服务终结点，请参阅[服务终结点概述](virtual-network-service-endpoints-overview.md)和[管理子网](virtual-network-manage-subnet.md)。

如果你的帐户中有多个虚拟网络，你可能需要在这些虚拟网络之间建立连接，使资源能够相互通信。 若要了解如何连接虚拟网络，请继续学习下一教程。

> [!div class="nextstepaction"]
> [连接虚拟网络](./tutorial-connect-virtual-networks-portal.md)
