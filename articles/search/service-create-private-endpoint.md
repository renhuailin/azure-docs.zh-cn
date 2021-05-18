---
title: 创建专用终结点以建立安全连接
titleSuffix: Azure Cognitive Search
description: 在虚拟网络中设置专用终结点以建立到 Azure 认知搜索服务的安全连接。
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 7445ac5d750ac29d3e6ce466a48e82efd1bcde40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545524"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>创建专用终结点以建立到 Azure 认知搜索的安全连接

在本文中，你将使用 Azure 门户创建无法通过 Internet 访问的新的 Azure 认知搜索服务实例。 接下来，你将在同一虚拟网络中配置 Azure 虚拟机，并使用它通过专用终结点访问搜索服务。

专用终结点由 [Azure 专用链接](../private-link/private-link-overview.md)作为单独的服务提供。 有关费用的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/private-link/)。

如本文中所述，你可以在 Azure 门户中创建专用终结点。 或者，你可以使用[管理 REST API 版本 2020-03-13](/rest/api/searchmanagement/)、[Azure PowerShell](/powershell/module/az.search) 或 [Azure CLI](/cli/azure/search)。

> [!NOTE]
> 当服务终结点为专用时，某些门户功能处于禁用状态。 你可以查看和管理服务级别信息，但出于安全原因，将隐藏索引、索引器和技能组信息。 可以使用 [VS Code 扩展](https://aka.ms/vscode-search)与服务中的各种组件进行交互，以此替代门户。

## <a name="why-use-a-private-endpoint-for-secure-access"></a>为何使用专用终结点进行安全访问？

Azure 认知搜索的[专用终结点](../private-link/private-endpoint-overview.md)允许虚拟网络上的客户端通过[专用链接](../private-link/private-link-overview.md)安全地访问搜索索引中的数据。 专用终结点将[虚拟网络地址空间](../virtual-network/private-ip-addresses.md)中的 IP 地址用于你的搜索服务。 客户端与搜索服务之间的网络流量将穿过虚拟网络以及 Microsoft 主干网络上的专用链接，不会从公共 Internet 公开。 有关支持专用链接的其他 PaaS 服务的列表，请查看产品文档中的[“可用性”](../private-link/private-link-overview.md#availability)部分。

为搜索服务使用专用终结点，你可以：

- 阻止在搜索服务的公共终结点上的所有连接。
- 阻止数据从虚拟网络泄露，从而提高虚拟网络的安全性。
- 使用 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoutes](../expressroute/expressroute-locations.md) 通过专用对等互连从连接到虚拟网络的本地网络安全地连接到你的搜索服务。

## <a name="create-the-virtual-network"></a>创建虚拟网络

在本部分中，你将创建虚拟网络和子网来承载用于访问搜索服务的专用终结点的 VM。

1. 在 Azure 门户的主页选项卡上，选择“创建资源” > “网络” > “虚拟网络”。  

1. 在“创建虚拟网络”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 订阅 | 选择订阅|
    | 资源组 | 选择“新建”，输入“myResourceGroup”，然后选择“确定” |
    | 名称 | 输入“MyVirtualNetwork” |
    | 区域 | 选择所需的区域 |
    |||

1. 至于其余设置，请保留默认值。 单击“查看 + 创建”，然后单击“创建” 。

## <a name="create-a-search-service-with-a-private-endpoint"></a>创建具有专用终结点的搜索服务

在本部分中，你将创建具有专用终结点的新的 Azure 认知搜索服务。 

1. 在 Azure 门户中屏幕的左上方，选择“创建资源” > “Web” > “Azure 认知搜索”。  

1. 在“新建搜索服务 - 基本信息”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | URL | 输入唯一名称。 |
    | 位置 | 选择所需的区域。 |
    | 定价层 | 选择“更改定价层”并选择所需的服务层级。 （不支持“免费”层。 必须是“基本”或更高层。） |
    |||
  
1. 在完成时选择“下一步:**缩放”** 。

1. 将值保留为默认值，然后选择“下一步:网络”。

1. 在“新建搜索服务 - 网络”中，针对“终结点连接性(数据)”选择“专用”  。

1. 在“新建搜索服务 - 网络”中的“专用终结点”下选择“+ 添加”。  

1. 在“创建专用终结点”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    | 位置 | 选择“美国西部”。|
    | 名称 | 输入“myPrivateEndpoint”。  |
    | 目标子资源 | 保留默认值“searchService”。 |
    | **网络** |  |
    | 虚拟网络  | 从资源组“myResourceGroup”中选择“MyVirtualNetwork”。 |
    | 子网 | 选择“mySubnet”。 |
    | **专用 DNS 集成** |  |
    | 与专用 DNS 区域集成  | 保留默认值“是”。 |
    | 专用 DNS 区域  | 保留默认值“(新建) privatelink.search.windows.net”。 |
    |||

1. 选择“确定”。 

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。 

1. 看到“验证通过”消息时，选择“创建” 。 

1. 新服务的预配完成后，浏览到刚创建的资源。

1. 从左侧内容菜单中选择“密钥”。

1. 复制 **主管理密钥** 供以后连接到服务时使用。

## <a name="create-a-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “计算” > “虚拟机”。  

1. 在“创建虚拟机 - 基本信息”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。  |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 *myVm*。 |
    | 区域 | 选择“美国西部”或你使用的任何其他区域。 |
    | 可用性选项 | 保留默认值“不需要基础结构冗余”。 |
    | 映像 | 选择“Windows Server 2019 Datacenter”。 |
    | 大小 | 保留默认值“标准 DS1 v2”。 |
    | **管理员帐户** |  |
    | 用户名 | 输入所选用户名。 |
    | 密码 | 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 确认密码 | 重新输入密码。 |
    | **入站端口规则** |  |
    | 公共入站端口 | 保留默认设置“允许所选端口”。 |
    | 选择入站端口 | 保留默认值“RDP (3389)”。 |
    | **节省资金** |  |
    | 已有 Windows 许可证？ | 保留默认值“否”。 |
    |||

1. 在完成时选择“下一步:**磁盘”** 。

1. 在“创建虚拟机 - 磁盘”中保留默认值，然后选择“下一步: **网络”** 。

1. 在“创建虚拟机 - 基本信息”中，选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 虚拟网络 | 保留默认值“MyVirtualNetwork”。  |
    | 地址空间 | 保留默认值“10.1.0.0/24”。|
    | 子网 | 保留默认值“mySubnet (10.1.0.0/24)”。|
    | 公共 IP | 保留默认值“(new) myVm-ip”。 |
    | 公共入站端口 | 选择“允许所选端口”  。 |
    | 选择入站端口 | 选择“HTTP”和“RDP”。 |
    ||

   > [!NOTE]
   > IPv4 地址可以表示为 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 格式。 请记住，应避免使用为专用网络保留的 IP 范围，如 [RFC 1918](https://tools.ietf.org/html/rfc1918) 中所述：
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

1. 看到“验证通过”消息时，选择“创建” 。 

## <a name="connect-to-the-vm"></a>连接到 VM

下载，然后连接到 VM myVm，如下所述：

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开 。

1. 选择“下载 RDP 文件”。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开下载的 .rdp* 文件。

    1. 出现提示时，选择“连接”。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据 。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续” 。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="test-connections"></a>测试连接

在本部分中，你将验证对搜索服务的专用网络访问并使用专用终结点以专用方式进行连接。

当搜索服务终结点为专用时，某些门户功能处于禁用状态。 你将能够查看和管理服务级别设置，但出于安全方面的考虑，已限制对索引数据以及此服务中的各种其他组件（如索引、索引器和技能组定义）的门户访问。

1. 在 *myVM* 的远程桌面中，打开 PowerShell。

1. 输入“nslookup [search service name].search.windows.net”

    将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. 从 VM 中，连接到搜索服务并创建索引。 可以按照此[快速入门](search-get-started-rest.md)使用 REST API 在你的服务中创建新的搜索索引。 通过 Web API 测试工具设置请求需要使用搜索服务终结点 (https://[搜索服务名称].search.windows.net) 和你在之前的步骤中复制的管理 api-key。

1. 从 VM 完成快速入门便可确认服务是否可以完全运行。

1. 关闭与 *myVM* 的远程桌面连接。 

1. 若要验证你的服务在公共终结点上是否不可访问，请在本地工作站上打开 Postman，并尝试执行快速入门中的前几个任务。 如果收到一条错误，指出远程服务器不存在，则表明已成功为你的搜索服务配置了专用终结点。

## <a name="clean-up-resources"></a>清理资源 
用完专用终结点、搜索服务和 VM 之后，请删除资源组及其包含的所有资源：
1. 在门户顶部的“搜索”框中输入“myResourceGroup” **  ，然后从搜索结果中选择“myResourceGroup”。 **   
1. 选择“删除资源组”。 
1. 对于“键入资源组名称”，请输入  *myResourceGroup* ，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤
在本文中，你已在虚拟网络上创建了一个 VM，并创建了具有专用终结点的搜索服务。 你从 Internet 连接到了该 VM，并使用专用链接安全地与该搜索服务进行了通信。 若要详细了解专用终结点，请参阅 [什么是 Azure 专用终结点？](../private-link/private-endpoint-overview.md)。
