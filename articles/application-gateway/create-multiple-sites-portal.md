---
title: 教程：使用 Azure 门户托管多个网站
titleSuffix: Azure Application Gateway
description: 本教程介绍了如何使用 Azure 门户创建托管多个网站的应用程序网关。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: 8de0dd16c3b3b73b5d5e52017cf63b357654144f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030951"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>教程：使用 Azure 门户创建和配置托管多个网站的应用程序网关

创建[应用程序网关](overview.md)时，可以使用 Azure 门户[配置多个网站的托管](multiple-site-overview.md)。 本教程使用虚拟机定义后端地址池。 然后，基于两个域配置侦听器和规则，以确保 Web 流量可到达池中的相应服务器。 本教程使用 *www.contoso.com* 和 *www.fabrikam.com* 示例。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建应用程序网关
> * 为后端服务器创建虚拟机
> * 使用后端服务器创建后端池
> * 创建侦听器
> * 创建路由规则
> * 编辑主机文件以进行名称解析

:::image type="content" source="./media/create-multiple-sites-portal/scenario.png" alt-text="多站点应用程序网关":::

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-an-application-gateway"></a>创建应用程序网关

1. 选择 Azure 门户左侧菜单上的“创建资源”。 此时会显示“新建”窗口。

2. 选择“网络”，然后在“特色”列表中选择“应用程序网关” 。

### <a name="basics-tab"></a>“基本信息”选项卡

1. 在“基本信息”选项卡上，输入这些值作为以下应用程序网关设置：

   - **资源组**：选择 **myResourceGroupAG** 作为资源组。 如果该资源组不存在，请选择“新建”，创建一个新的。
   - **应用程序网关名称**：输入 *myAppGateway* 作为应用程序网关的名称。

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png" alt-text="创建应用程序网关":::

2.  Azure 需要一个虚拟网络才能在创建的资源之间通信。 可以创建新的虚拟网络，或者使用现有的虚拟网络。 在此示例中，将在创建应用程序网关的同时创建新的虚拟网络。 在独立的子网中创建应用程序网关实例。 在本示例中创建两个子网：一个用于应用程序网关，另一个用于后端服务器。

    在“配置虚拟网络”下，选择“新建”以创建新的虚拟网络   。 在打开的“创建虚拟网络”窗口中，输入以下值以创建虚拟网络和两个子网  ：

    - **名称**：输入 *myVNet* 作为虚拟网络的名称。

    - **子网名称**（应用程序网关子网）：子网网关将显示名为“默认值”的子网。 将此子网的名称更改为 myAGSubnet。<br>应用程序网关子网只能包含应用程序网关。 不允许其他资源。

    - **子网名称**（后端服务器子网）：在子网网关的第二行中，在“子网名称”列输入“myBackendSubnet”。

    - **地址范围**（后端服务器子网）：在子网网格的第二行中，输入不会与 myAGSubnet 的地址范围重叠的地址范围。 例如，如果 myAGSubnet 的地址范围为 10.0.0.0/24，则为 myBackendSubnet 的地址范围输入 10.0.1.0/24  。

    选择“确定”以关闭“创建虚拟网络”窗口，并保存虚拟网络设置   。

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png" alt-text="创建 VNet":::
    
3. 在“基本信息”  选项卡上，接受其他设置的默认值，然后选择“下一步:  前端”。

### <a name="frontends-tab"></a>“前端”选项卡

1. 在“前端”选项卡上，验证“IP 地址类型”是否设置为“公共”  。 <br>可根据用例将前端 IP 配置为公共或专用 IP。 本示例将选择公共前端 IP。
   > [!NOTE]
   > 对于应用程序网关 v2 SKU，只能选择 **公共** 前端 IP 配置。 目前尚未为此 v2 SKU 启用专用前端 IP 配置。

2. 为“公共 IP 地址”选择“新增”，输入“myAGPublicIPAddress”作为公共 IP 地址名称，然后选择“确定” 。 

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png" alt-text="创建另一个 VNet":::

3. 在完成时选择“下一步:**后端**。

### <a name="backends-tab"></a>“后端”选项卡

后端池用于将请求路由到为请求提供服务的后端服务器。 后端池可以包括 NIC、虚拟机规模集、公共 IP、内部 IP、完全限定的域名 (FQDN) 和多租户后端（例如 Azure 应用服务）。 在此示例中，将使用应用程序网关创建空的后端池，然后将后端目标添加到后端池。

1. 在“后端”选项卡上，选择“添加后端池” 。

2. 在打开的“添加后端池”窗口中，输入以下值以创建空的后端池  ：

    - **Name**：输入“contosoPool”  作为后端池的名称。
    - **添加不包含目标的后端池**：选择“是”以创建不包含目标的后端池  。 你将在创建应用程序网关之后添加后端目标。

3. 在“添加后端池”窗口中，选择“添加”以保存后端池配置并返回到“后端”选项卡  。
4. 现在，像添加前一个池一样，添加名为 *fabrikamPool* 的另一个后端池。
1. 选择 **添加** 。

    :::image type="content" source="./media/create-multiple-sites-portal/backend-pools.png" alt-text="创建后端":::

4. 在“后端”  选项卡上，选择“下一步:  配置”。

### <a name="configuration-tab"></a>配置选项卡

在“配置”选项卡上，将连接使用传递规则创建的前端和后端池  。

1. 选择“传递规则”列中的“添加传递规则” 。

2. 在打开的“添加传递规则”窗口中，输入“contosoRule”作为规则名称    。

3. 传递规则需要侦听器。 在“添加传递规则”窗口中的“侦听器”选项卡上，输入侦听器的以下值 ：

    - **规则名称**：*contosoRule*。
    - **侦听器名称**：*contosoListener*。
    - **前端 IP**：选择“公共”，以选择为前端创建的公共 IP  。

   在“其他设置”  下：
   - **侦听器类型**：多个站点
   - **主机名**：**www.contoso.com**

   接受“侦听器”选项卡上其他设置的默认值，然后选择“后端目标”选项卡以配置剩余的传递规则   。

   :::image type="content" source="./media/create-multiple-sites-portal/routing-rule.png" alt-text="创建传递规则":::

4. 在“后端目标”选项卡上，选择“contosoPool”为“后端目标”    。

5. 对于“HTTP 设置”，请选择“新建”以添加新的 HTTP 设置 。 HTTP 设置将决定传递规则的行为。 在打开的“添加 HTTP 设置”窗口中，”输入“contosoHTTPSetting”作为“HTTP 设置名称”    。 接受“添加 HTTP 设置”窗口中其他设置的默认值，然后选择“添加”以返回到“添加传递规则”窗口    。 

6. 在“添加传递规则”窗口上，选择“添加”以保存传递规则并返回到“配置”选项卡    。
7. 选择“添加路由规则”，并为 Fabrikam 添加类似的规则、侦听器、后端目标和 HTTP 设置。

     :::image type="content" source="./media/create-multiple-sites-portal/fabrikam-rule.png" alt-text="Fabrikam 规则":::

7. 在完成时选择“下一步:  标记”，然后选择“下一步:  查看 + 创建”。

### <a name="review--create-tab"></a>“查看 + 创建”选项卡

复查“查看 + 创建”选项卡上的设置，然后选择“创建”以创建虚拟网络、公共 IP 地址和应用程序网关   。 Azure 可能需要数分钟时间来创建应用程序网关。

请等待部署成功完成，然后再前进到下一部分。

## <a name="add-backend-targets"></a>添加后端目标

本示例将使用虚拟机作为目标后端。 可以使用现有的虚拟机，或创建新的虚拟机。 将创建两个虚拟机，供 Azure 用作应用程序网关的后端服务器。

若要添加后端目标，需要：

1. 创建两个新的 VM（contosoVM 和 fabrikamVM），用作后端服务器   。
2. 可以在虚拟机上安装 IIS，以验证是否已成功创建了应用程序网关。
3. 将后端服务器添加到后端池。

### <a name="create-a-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户中，选择“创建资源”。  此时会显示“新建”窗口。
2. 在“常用”列表中选择“Windows Server 2016 Datacenter” 。 此时会显示“创建虚拟机”页。<br>应用程序网关可将流量路由到其后端池中使用的任何类型的虚拟机。 本示例使用 Windows Server 2016 Datacenter。
3. 对于以下虚拟机设置，请在“基本信息”选项卡中输入相应值：

    - **订阅**：选择订阅。
    - **资源组**：选择 **myResourceGroupAG** 作为资源组名称。
    - **虚拟机名称**：输入“contosoVM”  作为虚拟机的名称。
    - **区域**：选择前面使用的同一区域。
    - **用户名**：为管理员用户名输入一个名称。
    - 密码：输入管理员的密码。
1. 接受其他默认值，然后选择“下一步:**磁盘”** 。  
2. 接受“磁盘”**选项卡的默认值**，然后选择“下一步:**网络”** 。
3. 在“网络”选项卡上，验证是否已选择 **myVNet** 作为 **虚拟网络**，以及是否已将“子网”设置为 **myBackendSubnet**。 接受其他默认值，然后选择“下一步:**管理”** 。<br>应用程序网关可与其所在的虚拟网络外部的实例进行通信，但需要确保已建立 IP 连接。
4. 在“管理”选项卡上，将“启动诊断”设置为“禁用”  。 接受其他默认值，然后选择“复查 + 创建”。
5. 在“复查 + 创建”选项卡上复查设置，更正任何验证错误，然后选择“创建”。
6. 等待虚拟机创建完成，然后再继续操作。

### <a name="install-iis-for-testing"></a>安装 IIS 用于测试

本示例在虚拟机上安装 IIS，只为验证 Azure 是否已成功创建应用程序网关。

1. 打开 [Azure PowerShell](../cloud-shell/quickstart-powershell.md)。 为此，请在 Azure 门户的顶部导航栏中选择“Cloud Shell”，然后从下拉列表中选择“PowerShell”。 

    ![安装自定义扩展](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. 运行以下命令以在虚拟机上安装 IIS，并将 <location\> 替换为自己的资源组区域： 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location <location>
    ```

3. 使用以前完成的步骤创建第二个虚拟机并安装 IIS。 使用“fabrikamVM”  作为虚拟机名称和 **Set-AzVMExtension** cmdlet 的 **VMName** 设置。

### <a name="add-backend-servers-to-backend-pools"></a>将后端服务器添加到后端池

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 从左侧菜单中选择“后端池”。

3. 选择 **contosoPool**。

4. 在“目标”类型下，从下拉列表中选择“虚拟机” 。

5. 在“目标”下，从下拉列表中选择 **contosoVM** 虚拟机的网络接口。

    ![添加后端服务器](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. 选择“保存”。
7. 重复此步骤，将 fabrikamVM  和接口添加到 fabrikamPool  。

等待部署完成之后再继续下一步。

## <a name="edit-your-hosts-file-for-name-resolution"></a>编辑主机文件以进行名称解析

使用其公共 IP 地址创建应用程序网关后，可以获取 IP 地址，并使用它来编辑主机文件，用于解析 `www.contoso.com` 和 `www.fabrikam.com`。 在生产环境中，可以在 DNS 中创建 `CNAME` 以进行名称解析。

1. 单击“所有资源”  ，然后单击“myAGPublicIPAddress”  。

    ![记下应用程序网关的 DNS 地址](./media/create-multiple-sites-portal/public-ip.png)

2. 复制 IP 地址并将其用作 `hosts` 文件中新条目的值。
1. 在本地计算机上打开管理命令提示符，然后导航到 `c:\Windows\System32\drivers\etc`。
1. 打开 `hosts` 文件并添加以下条目，其中 `x.x.x.x` 是应用程序网关的公共 IP 地址：
   ```dos
   # Copyright (c) 1993-2009 Microsoft Corp.
   #
   # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
   #
   # This file contains the mappings of IP addresses to host names. Each
   # entry should be kept on an individual line. The IP address should
   # be placed in the first column followed by the corresponding host name.
   # The IP address and the host name should be separated by at least one
   # space.
   #
   # Additionally, comments (such as these) may be inserted on individual
   # lines or following the machine name denoted by a '#' symbol.
   #
   # For example:
   #
   #      102.54.94.97     rhino.acme.com          # source server
   #       38.25.63.10     x.acme.com              # x client host
   
   # localhost name resolution is handled within DNS itself.
   #    127.0.0.1       localhost
   #    ::1             localhost
   x.x.x.x www.contoso.com
   x.x.x.x www.fabrikam.com

   ```
1. 保存文件。
1. 运行以下命令以加载并显示对 hosts 文件的更改：
   ```dos
    ipconfig/registerdns
    ipconfig/displaydns
   ```
   
## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 在浏览器的地址栏中键入一个域名。 例如 `http://www.contoso.com`。

    ![在应用程序网关中测试 contoso 站点](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. 将地址更改为其他域，然后应会看到以下示例所示的内容：

    ![在应用程序网关中测试 fabrikam 站点](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过应用程序网关创建的资源，请删除资源组。 删除资源组时，也会删除应用程序网关和及其所有的相关资源。

若要删除资源组，请执行以下操作：

1. 在 Azure 门户的左侧菜单上选择“资源组”  。
2. 在“资源组”页的列表中搜索“myResourceGroupAG”，然后将其选中。
3. 在“资源组”页上，选择“删除资源组”   。
4. 在“键入资源组名称”字段中输入“myResourceGroupAG”，然后选择“删除”。 

若要还原 hosts 文件：
1. 删除 hosts 文件中的 `www.contoso.com` 和 `www.fabrikam.com` 行，然后在命令提示符下运行 `ipconfig/registerdns` 和 `ipconfig/flushdns`。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure 应用程序网关的作用](./overview.md)
