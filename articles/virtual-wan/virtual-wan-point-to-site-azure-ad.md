---
title: 为用户 VPN 连接配置 Azure AD 身份验证：虚拟 WAN
description: 了解如何为虚拟 WAN 用户 VPN（点到站点）配置 Azure Active Directory 身份验证。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alzam
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 59b93327aae8a400b4d1ab6c9ea3f67e5bd9dd03
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110703366"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>为用户 VPN 配置 Azure Active Directory 身份验证

本文介绍如何为虚拟 WAN 中的用户 VPN 配置 Azure AD 身份验证，以通过 OpenVPN VPN 连接与 Azure 中的资源建立连接。 Azure Active Directory 身份验证仅适用于使用 OpenVPN 协议的网关以及运行 Windows 的客户端。

此类连接要求在客户端计算机上配置一个客户端。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。

在本文中，学习如何：

* 创建虚拟 WAN
* 创建虚拟中心
* 创建用户 VPN 配置
* 下载虚拟 WAN 用户 VPN 配置文件
* 将用户 VPN 配置应用于虚拟中心
* 将 VNet 连接到虚拟中心
* 下载并应用用户 VPN 客户端配置
* 查看虚拟 WAN

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>准备阶段

在开始配置之前，请验证是否符合以下条件：

* 你拥有一个要连接到的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

* 虚拟网络不包含任何虚拟网络网关。 如果虚拟网络包含网关（VPN 或 ExpressRoute），则必须删除所有网关。 此配置要求将虚拟网络改为连接到虚拟 WAN 中心网关。

* 获取中心区域的 IP 地址范围。 该中心是虚拟 WAN 创建和使用的虚拟网络。 为中心指定的地址范围不能与要连接到的任何现有虚拟网络重叠。 此外，它也不能与本地连接到的地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-wan"></a><a name="wan"></a>创建虚拟 WAN

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

1. 导航到“虚拟 WAN”页。 在门户中，单击“+创建资源”。 在搜索框中键入“虚拟 WAN”，然后选择 Enter。
2. 从结果中选择“虚拟 WAN”。 在“虚拟 WAN”页上，单击“创建”以打开“创建 WAN”页。
3. 在“创建 WAN”页的“基本信息”选项卡上，填写以下字段   ：

   ![虚拟 WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **订阅** - 选择要使用的订阅。
   * **资源组** - 新建资源组或使用现有的资源组。
   * **资源组位置** - 从下拉列表中选择资源位置。 WAN 是一个全局资源，不会驻留在某个特定区域。 但是，必须选择一个区域才能更轻松地管理和查找所创建的 WAN 资源。
   * **名称** - 键入要用于称呼 WAN 的名称。
   * **类型：** 标准。 如果创建基本 WAN，则只能创建基本中心。 基本中心仅支持 VPN 站点到站点连接。
4. 填写完字段后，单击“审阅 + 创建”。
5. 验证通过后，选择“创建”以创建虚拟 WAN。

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>创建空虚拟中心

1. 在虚拟 WAN 下选择“中心”，然后单击“+新建中心”。

   ![屏幕截图显示了中心配置对话框，其中已选择“新建中心”。](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. 在“创建虚拟中心”页上，请填写以下字段。

   **区域** - 选择要在其中部署虚拟中心的区域。

   **名称** - 输入要用于称呼虚拟中心的名称。

   **中心专用地址空间** - 用 CIDR 表示法来表示的中心地址范围。

   ![屏幕截图显示了可以在其中输入值的“新建虚拟中心”窗格。](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. 单击“查看 + 创建”。
4. 在“验证已通过”页上，单击“创建”。 

## <a name="create-a-new-user-vpn-configuration"></a><a name="site"></a>新建用户 VPN 配置

用户 VPN 配置定义连接远程客户端的参数。

1. 在虚拟 WAN 下，选择“用户 VPN 配置”。

   ![屏幕截图显示了已选择“用户 VPN 配置”菜单项。](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. 单击“+创建用户 VPN 配置”。

   ![屏幕截图显示了“创建用户 VPN 配置”链接。](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. 在“基本”下，指定以下参数
   * **配置名称** - 输入要用来调用用户 VPN 配置的名称。
    * **隧道类型** - 从下拉菜单中选择“OpenVPN”。
4. 导航到“Azure Active Directory”。 将“Azure Active Directory”切换为“是”，并基于租户详细信息提供以下值。 
   * **身份验证方法** - 选择“Azure Active Directory”。
   * **受众** - 键入在 Azure AD 租户中注册的 [Azure VPN](openvpn-azure-ad-tenant.md) 企业应用程序的应用程序 ID。 
   * **颁发者** - `https://sts.windows.net/<your Directory ID>/`
   * **AAD 租户** - `https://login.microsoftonline.com/<your Directory ID>`
  
   ![屏幕截图显示了可在其中输入值的“新建用户 VPN 配置”窗格。](media/virtual-wan-point-to-site-azure-ad/configure-aad-profile.png)

## <a name="edit-hub-assignment"></a><a name="hub"></a>编辑中心分配

1. 导航到虚拟 WAN 下的“中心”边栏选项卡。 
2. 选择要将 VPN 服务器配置关联到的中心，然后单击省略号图标 (...)。

   ![屏幕截图显示从菜单选择了“编辑虚拟中心”。](media/virtual-wan-point-to-site-azure-ad/select-hub.png)
3. 单击“编辑虚拟中心”。
4. 选中“包括点到站点网关”复选框，然后选择所需的网关缩放单元。

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/edit-virtual-hub.png" alt-text="屏幕截图显示“编辑虚拟中心”对话框，可在该对话框中选择网关缩放单元。"lightbox="./media/virtual-wan-point-to-site-azure-ad/edit-virtual-hub.png":::

5. 输入用于为 VPN 客户端分配 IP 地址的“地址池”  。
6. 单击“确认”  。
7. 完成此操作最多需要 30 分钟。

## <a name="download-user-vpn-profile"></a><a name="device"></a>下载用户 VPN 配置文件

使用 VPN 配置文件来配置客户端。

1. 在虚拟 WAN 的页面上，单击“用户 VPN 配置”。
2. 在页面顶部，单击“下载用户 VPN 配置”。
3. 完成创建文件后，可以单击相应的链接下载该文件。
4. 使用此配置文件配置 VPN 客户端。

## <a name="configure-user-vpn-clients"></a>配置用户 VPN 客户端

若要进行连接，需要下载 Azure VPN 客户端，并在要连接到 VNet 的每台计算机上导入在前面步骤中下载的 VPN 客户端配置文件。

> [!NOTE]
> 仅 OpenVPN&reg; 协议连接支持 Azure AD 身份验证。
>

#### <a name="to-download-the-azure-vpn-client"></a>下载 Azure VPN 客户端

使用[此链接](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab)下载 Azure VPN 客户端。

#### <a name="to-import-a-client-profile"></a><a name="import"></a>导入客户端配置文件

1. 在页面上，选择“导入”。 

    ![屏幕截图显示从加号菜单选择了“导入”。](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. 浏览到 XML 配置文件并将其选中。 选择该文件后，选择“打开”。 

    ![屏幕截图显示了“打开”对话框，你可在其中选择文件。](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. 指定配置文件的名称，并选择“保存”。 

    ![屏幕截图显示已添加“连接名称”并且已选择“保存”按钮。](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. 选择“连接”以连接到 VPN。 

    ![屏幕截图显示了刚刚创建的连接的“连接”按钮。](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. 连接后，图标将变为绿色并指示“已连接”。 

    ![屏幕截图显示了处于“已连接”状态的连接以及用于断开连接的选项。](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>删除客户端配置文件

1. 选择要删除的客户端配置文件旁边的省略号图标 (...)。 然后选择“删除”  。

    ![屏幕截图显示从菜单选择了“删除”。](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. 选择“删除”以删除配置文件。 

    ![屏幕截图显示了确认对话框，其中包含“删除”或“取消”选项。](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>诊断连接问题

1. 若要诊断连接问题，可以使用“诊断”工具。  选择要诊断的 VPN 连接旁边的省略号图标 (...) 以显示菜单。 然后选择“诊断”。 

    ![屏幕截图显示从菜单选择了“诊断”。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. 在“连接属性”页上，选择“运行诊断”。  

    ![屏幕截图显示连接的“运行诊断”按钮。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. 使用凭据登录。

    ![屏幕截图显示此操作的“登录”对话框。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. 查看诊断结果。

    ![屏幕截图显示了诊断结果。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>查看虚拟 WAN

1. 导航到虚拟 WAN。
2. 在“概述”页上，地图中的每个点表示一个中心。
3. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="clean-up-resources"></a><a name="cleanup"></a>清理资源

不再需要这些资源时，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 删除资源组及其包含的所有资源。 将“myResourceGroup”替换为资源组的名称，并运行以下 PowerShell 命令：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
