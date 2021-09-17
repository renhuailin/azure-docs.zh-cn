---
title: 使用 Azure Active Directory 身份验证配置 P2S 用户 VPN 连接
titleSuffix: Azure Virtual WAN
description: 了解如何为虚拟 WAN 用户 VPN（点到站点）配置 Azure Active Directory 身份验证。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/20/2021
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 38006136ddd5a5331699b2768c35ebd794e563a8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778508"
---
# <a name="configure-a-point-to-site-user-vpn-connection---azure-active-directory-authentication"></a>配置点到站点用户 VPN 连接 - Azure Active Directory 身份验证

本文介绍如何为虚拟 WAN 中的用户 VPN 配置 Azure AD 身份验证，以通过 OpenVPN VPN 连接与 Azure 中的资源建立连接。 Azure Active Directory 身份验证仅适用于使用 OpenVPN 协议的网关。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

在本文中，学习如何：

* 创建虚拟 WAN
* 创建用户 VPN 配置
* 下载虚拟 WAN 用户 VPN 配置文件
* 创建虚拟中心
* 编辑中心以添加 P2S 网关
* 将 VNet 连接到虚拟中心
* 下载并应用用户 VPN 客户端配置
* 查看虚拟 WAN

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="虚拟 WAN 示意图。":::

## <a name="before-you-begin"></a>准备阶段

在开始配置之前，请验证你是否符合以下条件：

* 你拥有一个要连接到的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

* 虚拟网络不包含任何虚拟网络网关。 如果虚拟网络包含网关（VPN 或 ExpressRoute），则必须删除所有网关。 此配置要求将虚拟网络改为连接到虚拟 WAN 中心网关。

* 获取中心区域的 IP 地址范围。 该中心是虚拟 WAN 创建和使用的虚拟网络。 为中心指定的地址范围不能与要连接到的任何现有虚拟网络重叠。 此外，它也不能与本地连接到的地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-wan"></a><a name="wan"></a>创建虚拟 WAN

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="user-config"></a>创建用户 VPN 配置

用户 VPN 配置定义连接远程客户端的参数。 请务必在使用 P2S 设置配置虚拟中心之前创建用户 VPN 配置，因为必须指定要使用的用户 VPN 配置。

1. 导航到“虚拟 WAN -> 用户 VPN 配置”页，然后单击“+ 创建用户 VPN 配置”。 

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/user-vpn.png" alt-text="创建用户 VPN 配置的屏幕截图。":::
1. 在“基本”页上，指定参数。

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/basics.png" alt-text="“基本”页面的屏幕截图。":::

   * **配置名称** - 输入要用来调用用户 VPN 配置的名称。
    * **隧道类型** - 从下拉菜单中选择“OpenVPN”。
1. 单击“Azure Active Directory”，打开页面。

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/values.png" alt-text="Azure Active Directory 页的屏幕截图。":::

    将“Azure Active Directory”切换为“是”，并基于租户详细信息提供以下值。  可以在门户中企业应用程序的“Azure Active Directory”页上查看必要的值。
   * **身份验证方法** - 选择“Azure Active Directory”。
   * **受众** - 键入在 Azure AD 租户中注册的 [Azure VPN](openvpn-azure-ad-tenant.md) 企业应用程序的应用程序 ID。 
   * **颁发者** - `https://sts.windows.net/<your Directory ID>/`
   * **AAD 租户** - `https://login.microsoftonline.com/<your Directory ID>`
1. 单击“创建”以创建用户 VPN 配置。 稍后将在练习中选择此配置。

## <a name="create-an-empty-hub"></a><a name="site"></a>创建空白的中心

在本练习中，我们创建一个空的虚拟中心。 在下一部分中，将向现有中心添加网关。 但是，也可以将这些步骤结合起来，一次性创建具有 P2S 网关设置的中心。

[!INCLUDE [Create an empty hub](../../includes/virtual-wan-empty-hub-include.md)]

## <a name="add-a-p2s-gateway-to-a-hub"></a><a name="hub"></a>将 P2S 网关添加到中心

本节演示如何将网关添加到现有的虚拟中心。 此步骤最多可能需要 30 分钟才能让中心完成更新。

1. 导航到虚拟 WAN 下的“中心”页。
1. 选择要将 VPN 服务器配置关联到的中心，然后单击省略号 (...) 以显示菜单。 然后单击“编辑虚拟中心”。

   :::image type="content" source="media/virtual-wan-point-to-site-azure-ad/select-hub.png" alt-text="屏幕截图显示从菜单选择了“编辑虚拟中心”。" lightbox="media/virtual-wan-point-to-site-azure-ad/select-hub.png":::

1. 在“编辑虚拟中心”页上，选中“包含 VPN 站点的 VPN 网关”和“包含点到站点网关”的复选框以显示设置。   然后配置这些值。

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/edit-virtual-hub.png" alt-text="显示“编辑虚拟中心”页的屏幕截图。":::

   * 网关缩放单元：选择“网关缩放单元”。 缩放单元表示用户 VPN 网关的聚合容量。 如果选择 40 或更多网关缩放单元，请相应地规划客户端地址池。 有关此设置如何影响客户端地址池的信息，请参阅[关于客户端地址池](about-client-address-pools.md)。 有关网关缩放单元的信息，请参阅[常见问题解答](virtual-wan-faq.md#for-user-vpn-point-to-site--how-many-clients-are-supported)。
   * 用户 VPN 配置：选择之前创建的配置。
   * 客户端地址池：指定用于为 VPN 客户端分配 IP 地址的客户端地址池。 此设置对应于选择的网关缩放单元 
1. 单击“确认”  。 更新中心最多可能需要 30 分钟。

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>将 VNet 连接到中心

在本部分中，将创建虚拟中心与 VNet 之间的连接。

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-user-vpn-profile"></a><a name="device"></a>下载用户 VPN 配置文件

VPN 客户端的所有必需配置设置都包含在 VPN 客户端 zip 配置文件中。 zip 文件中的设置有助于轻松配置 VPN 客户端。 生成的 VPN 客户端配置文件特定于网关的用户 VPN 配置。 在本部分中，将生成并下载用于配置 VPN 客户端的文件。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-user-vpn-clients"></a>配置用户 VPN 客户端

连接的每台计算机都必须安装客户端。 可以使用在前面步骤中下载的 VPN 用户客户端配置文件来配置每个客户端。 阅读与要连接的操作系统有关的文章。

### <a name="to-configure-macos-vpn-clients-preview"></a>配置 macOS VPN 客户端（预览版）

有关 macOS 客户端说明，请参阅[配置 VPN 客户端 - macOS（预览版）](openvpn-azure-ad-client-mac.md)。

### <a name="to-configure-windows-vpn-clients"></a>配置 Windows VPN 客户端

[!INCLUDE [Download Azure VPN client](../../includes/vpn-gateway-download-vpn-client.md)]

#### <a name="to-import-a-vpn-client-profile-windows"></a><a name="import"></a>导入 VPN 客户端配置文件 (Windows)

1. 在页面上，选择“导入”。 

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/import/import-1.png" alt-text="显示导入页的屏幕截图。":::

1. 浏览到 XML 配置文件并将其选中。 选择该文件后，选择“打开”。 

    ![屏幕截图显示了“打开”对话框，你可在其中选择文件。](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

1. 指定配置文件的名称，并选择“保存”。 

    ![屏幕截图显示已添加“连接名称”并且已选择“保存”按钮。](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

1. 选择“连接”以连接到 VPN。 

    ![屏幕截图显示了刚刚创建的连接的“连接”按钮。](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

1. 连接后，图标将变为绿色并指示“已连接”。 

    ![屏幕截图显示了处于“已连接”状态的连接以及用于断开连接的选项。](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile---windows"></a><a name="delete"></a>删除客户端配置文件 - Windows

1. 选择要删除的客户端配置文件旁边的省略号图标 (...)。 然后选择“删除”  。

    ![屏幕截图显示从菜单选择了“删除”。](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

1. 选择“删除”以删除配置文件。 

    ![屏幕截图显示了确认对话框，其中包含“删除”或“取消”选项。](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues---windows"></a><a name="diagnose"></a>诊断连接问题 - Windows

1. 若要诊断连接问题，可以使用“诊断”工具。  选择要诊断的 VPN 连接旁边的省略号图标 (...) 以显示菜单。 然后选择“诊断”。 

    ![屏幕截图显示从菜单选择了“诊断”。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

1. 在“连接属性”页上，选择“运行诊断”。  

    ![屏幕截图显示连接的“运行诊断”按钮。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

1. 使用凭据登录。

    ![屏幕截图显示此操作的“登录”对话框。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

1. 查看诊断结果。

    ![屏幕截图显示了诊断结果。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>查看虚拟 WAN

1. 导航到虚拟 WAN。
2. 在“概述”页上，地图中的每个点表示一个中心。
3. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="clean-up-resources"></a><a name="cleanup"></a>清理资源

当不再需要所创建的资源时，请将其删除。 由于存在依赖关系，必须按特定顺序删除某些虚拟 WAN 资源。 大约需要 30 分钟才能完成删除。

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
