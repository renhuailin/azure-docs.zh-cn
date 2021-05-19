---
title: 配置内部负载均衡器 (ILB) 终结点
titleSuffix: Azure Application Gateway
description: 本文提供有关如何使用专用前端 IP 地址配置应用程序网关的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: 224cbe1e34e5915a7fa5fc1cf415c35f86c3abe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711648"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>使用内部负载均衡器 (ILB) 终结点配置应用程序网关

可以在 Azure 应用程序网关上配置面向 Internet 的 VIP 或不向 Internet 公开的内部终结点。 内部终结点使用前端（也称为内部负载均衡器 (ILB) 终结点）的专用 IP 地址。 

对于不向 Internet 公开的内部业务线应用程序，使用前端专用 IP 地址配置网关的做法非常有效。 对于多层应用程序中的服务和层也很有用，这些服务和层处于一个安全边界，不向 Internet 公开，但：

- 仍需要轮循负载分布
- 会话粘性
- 或传输层安全性 (TLS) 终止（旧称为“安全套接字层 (SSL)”）。

本文引导你完成在 Azure 门户中使用前端专用 IP 地址配置应用程序网关的步骤。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 Azure 门户 (<https://portal.azure.com>)

## <a name="create-an-application-gateway"></a>创建应用程序网关

Azure 需要一个虚拟网络才能在创建的资源之间通信。 创建新的虚拟网络，或者使用现有虚拟网络。 

本示例将创建新的虚拟网络。 可以在创建应用程序网关的同时创建虚拟网络。 在独立的子网中创建应用程序网关实例。 在本示例中有两个子网：一个用于应用程序网关，另一个用于后端服务器。

1. 展开门户菜单并选择“创建资源”。 
2. 选择“网络”  ，然后在“特色”列表中选择“应用程序网关”  。
3. 输入 *myAppGateway* 作为应用程序网关的名称，输入 *myResourceGroupAG* 作为新资源组的名称。
4. 对于“区域”，请选择“美国中部”。 
5. 对于“层”，请选择“标准”。  
6. 在“配置虚拟网络”下选择“新建”，然后输入虚拟网络的以下值：  
   - *myVNet* - 虚拟网络的名称。
   - *10.0.0.0/16* - 虚拟网络地址空间。
   - *myAGSubnet* - 子网名称。
   - *10.0.0.0/24* - 子网地址空间。
   - *myBackendSubnet* - 后端子网名称。
   - *10.0.1.0/24* - 后端子网地址空间。

    ![创建虚拟网络](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. 选择“确定”以创建虚拟网络和子网。
7. 选择“下一步: 前端”。
8. 对于“前端 IP 地址类型”，请选择“专用”。  

   默认采用动态 IP 地址分配。 所配置的子网的第一个可用地址将分配为前端 IP 地址。
   > [!NOTE]
   > IP 地址类型（静态或动态）一经分配，以后便不可更改。
9. 选择“下一步: 后端”。 
10. 选择“添加后端池”。 
11. 对于“名称”，请键入 *appGatewayBackendPool*。 
12. 对于“添加没有目标的后端池”，请选择“是”。   稍后将添加目标。
13. 选择“添加”   。
14. 选择“下一步: 配置”。 
15. 在“路由规则”下，选择“添加路由规则”。
16. 对于“规则名称”，请键入 *Rrule-01*。 
17. 对于“侦听器名称”，请键入 *Listener-01*。 
18. 对于“前端 IP”，请选择“专用”。  
19. 接受剩余的默认值，然后选择“后端目标”选项卡。 
20. 对于“目标类型”，请选择“后端池”，然后选择“appGatewayBackendPool”。   
21. 对于“HTTP 设置”，请选择“新增”。
22. 对于“HTTP 设置名称”，请键入 *http-setting-01*。 
23. 对于“后端协议”，请选择“HTTP”。  
24. 对于“后端端口”，请键入 *80*。 
25. 接受剩余的默认值，然后选择“添加”。 
26. 在“添加路由规则”页上，选择“添加”。  
27. 在完成时选择“下一步:  标记”。
28. 在完成时选择“下一步:  查看 + 创建”。
29. 检查摘要页上的设置，然后选择“创建”以创建网络资源和应用程序网关。  创建应用程序网关可能需要几分钟时间。 请等待部署成功完成，然后再前进到下一部分。

## <a name="add-backend-pool"></a>添加后端池

后端池用于将请求路由到为请求提供服务的后端服务器。 后端可以包含 NIC、虚拟机规模集、公共 IP 地址、内部 IP 地址、完全限定的域名 (FQDN) 和多租户后端（例如 Azure 应用服务）。 本示例将使用虚拟机作为目标后端。 可以使用现有的虚拟机，或创建新的虚拟机。 在此示例中，你创建两台虚拟机，供 Azure 用作应用程序网关的后端服务器。

为此，请执行以下操作：

1. 创建两个新虚拟机（*myVM* 和 *myVM2*）用作后端服务器。
2. 在虚拟机上安装 IIS，以验证是否成功创建了应用程序网关。
3. 将后端服务器添加到后端池。

### <a name="create-a-virtual-machine"></a>创建虚拟机


1. 选择“创建资源”。 
2. 依次选择“计算”、“虚拟机”。  
4. 输入虚拟机的以下值：
   - 选择订阅。
   - 对于“资源组”，请选择“myResourceGroupAG”。
   - 为“虚拟机名称”键入 myVM。
   - 对于“映像”，请选择“Windows Server 2019 Datacenter”。  
   - 键入有效“用户名”。
   - 键入有效“密码”。
1. 接受剩余的默认值，然后选择“下一步: 磁盘”。
1. 接受默认值，然后选择“下一步:  网络”。
1. 确保选择“myVNet”作为虚拟网络，子网是“myBackendSubnet”。
1. 接受剩余的默认值，然后选择“下一步:**管理”** 。
1. 选择“禁用”以禁止启动诊断。
1. 选择“查看 + 创建”。
1. 检查摘要页上的设置，然后选择“创建”。  创建 VM 可能需要几分钟时间。 请等待部署成功完成，然后再前进到下一部分。

### <a name="install-iis"></a>安装 IIS

1. 打开 Cloud Shell 并确保将其设置为“PowerShell”。
    ![屏幕截图显示使用 PowerShell 的一个已打开的 Azure Cloud Shell 控制台窗口。](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. 运行以下命令以在虚拟机上安装 IIS：

   ```azurepowershell
   Set-AzVMExtension `
        -ResourceGroupName myResourceGroupAG `
        -ExtensionName IIS `
        -VMName myVM `
        -Publisher Microsoft.Compute `
        -ExtensionType CustomScriptExtension `
        -TypeHandlerVersion 1.4 `
        -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
         -Location CentralUS 

   ```

3. 使用刚刚完成的步骤创建第二个虚拟机并安装 IIS。 为“虚拟机名称”和 `Set-AzVMExtension` 中的 `VMName` 使用“myVM2”。

### <a name="add-backend-servers-to-backend-pool"></a>将后端服务器添加到后端池

1. 选择“所有资源”，然后选择“myAppGateway”。  
2. 选择“后端池”，然后选择“appGatewayBackendPool”。
3. 在“目标类型”下选择“虚拟机”，然后在“目标”下选择与 myVM 关联的 vNIC。   
4. 重复此过程以添加 MyVM2。
   ![“编辑后端池”窗格，其中突出显示了“目标类型”和“目标”。](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. 选择“保存”。

## <a name="create-a-client-virtual-machine"></a>创建客户端虚拟机

客户端虚拟机用于连接到应用程序网关后端池。

- 使用前面的步骤创建第三个虚拟机。 使用“myVM3”作为虚拟机名称。

## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 在“myAppGateway”页上，选择“前端 IP 配置”以记下前端专用 IP 地址。
    ![“前端 IP 配置”窗格，其中突出显示了“专用”类型。](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. 复制专用 IP 地址，然后将其粘贴到 myVM3 上的浏览器地址栏中，以访问应用程序网关后端池。

## <a name="next-steps"></a>后续步骤

若要监视后端池的运行状况，请参阅[应用程序网关的后端运行状况和诊断日志](application-gateway-diagnostics.md)。
