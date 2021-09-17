---
title: 教程：在后端池中创建具有多个可用性集的负载均衡器 - Azure 门户
titleSuffix: Azure Load Balancer
description: 本教程将在后端池中创建具有多个可用性集的 Azure 负载均衡器。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/12/2021
ms.custom: template-tutorial
ms.openlocfilehash: 920cfa4053fac692145f46cc5cff7d53381d900b
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201794"
---
# <a name="tutorial-create-a-load-balancer-with-more-than-one-availability-set-in-the-backend-pool-using-the-azure-portal"></a>教程：在后端池中创建具有多个可用性集的负载均衡器

在部署高可用性过程中，通常将虚拟机分为多个可用性集。 

负载均衡器支持在后端池中部署多个包含虚拟机的可用性集。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络
> * 创建用于出站连接的 NAT 网关
> * 创建标准 SKU Azure 负载均衡器
> * 创建四个虚拟机和两个可用性集
> * 将可用性集中的虚拟机添加到负载均衡器的后端池
> * 测试负载均衡器

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-network"></a>创建虚拟网络

本部分将为本教程使用的负载均衡器和其他资源创建一个虚拟网络。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“虚拟网络”。

3. 在搜索结果中，选择“虚拟网络”。

4. 选择“+ 新建”。 

5. 在“创建虚拟网络”的“基本信息”选项卡中输入或选择以下信息 ：

    | 设置 | 值 |
    | ------- | ------|
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。 </br> 在“名称”中输入 TutorLBmultiAVS-rg 。 |
    | **实例详细信息** |   |
    | 名称 | 输入 **myVNet**。 |
    | 区域 | 选择“(US) 美国西部 2”。 |

6. 选择“IP 地址”选项卡，或选择页面底部的“下一页: IP 地址”按钮 。

7. 在“子网名称”下的“IP 地址”选项卡中，选择“默认值”  。

8. 在“子网名称”下的“编辑子网”窗格中，输入 myBackendSubnet  。

9. 选择“保存”。

10. 选择“安全性”选项卡，或者选择“下一页: 安全性”按钮（位于页面底部） 。

11. 在“安全性”选项卡的 BastionHost 中，选择“启用”  。

12. 输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | Bastion 名称 | 输入“MyBastionHost”。 |
    | AzureBastionSubnet 地址空间 | 输入 10.1.1.0/27。 |
    | 公共 IP 地址 | 选择“新建”。 </br> 在“名称”中输入“myBastionIP” 。 |

13. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”蓝色按钮 。

14. 选择“创建”。

## <a name="create-nat-gateway"></a>创建 NAT 网关 

此部分将为虚拟机的出站连接创建 NAT 网关。

1. 在门户顶部的搜索框中，输入“NAT 网关”。

2. 在搜索结果中选择“NAT 网关”。

3. 选择“+ 新建”。 

4. 在“创建网络地址转换(NAT)网关”的“基本信息”选项卡中，输入或选择以下值： 

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择 TutorLBmultiAVS-rg。 |
    | **实例详细信息** |   |
    | NAT 网关名称 | 输入 myNATGateway。 |
    | 区域 | 选择“(US) 美国西部 2”。 |
    | 可用性区域 | 选择“无”。 |
    | 空闲超时（分钟） | 输入 **15**。 |

5. 选择“出站 IP”选项卡，或者选择“下一步:出站 IP”按钮（位于页面底部） 。

6. 在“出站 IP”选项卡中，选择“公共 IP 地址”旁边的“创建新的公共 IP 地址”  。

7. 在“名称”中输入“myNATgatewayIP” 。

8. 选择“确定”。

9. 选择“子网”选项卡，或者选择“下一步:子网”按钮（位于页面底部） 。

10. 在“虚拟网络”下的下拉菜单中，选择“myVNet” 。

11. 选中 myBackendSubnet 旁边的复选框。

12. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

13. 选择“创建”。

## <a name="create-load-balancer"></a>创建负载均衡器

本部分将为虚拟机创建负载均衡器。

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 在“负载均衡器”页上，选择“创建” 。

3. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | **项目详细信息** |   |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择 TutorLBmultiAVS-rg。 |
    | **实例详细信息** |   |
    | 名称                   | 输入“myLoadBalancer”                                   |
    | 区域         | 选择“(US) 美国西部 2”。                                        |
    | 类型          | 选择“公共”。                                        |
    | SKU           | 保留默认值“标准”。 |
    | 层          | 保留默认值“区域”。 |

4. 在页面底部选择“下一页: 前端 IP 配置”。

5. 在“前端 IP 配置”中，选择“+ 添加前端 IP” 。

6. 在“名称”中输入“LoadBalancerFrontEnd” 。

7. 对于“IP 版本”，请选择“IPv4”或“IPv6”  。

    > [!NOTE]
    > IPv6 目前不支持路由首选项或跨区域负载平衡（全局层）。

8. 选择“IP 类型”的“IP 地址” 。

    > [!NOTE]
    > 有关 IP 前缀的更多详细信息，请参阅 [Azure 公共 IP 地址前缀](../virtual-network/public-ip-address-prefix.md)。

9. 在“公共 IP 地址”中选择“新建” 。

10. 在“添加公共 IP”的“名称”中输入“myPublicIP-lb”  。

11. 在“可用性区域”中选择“区域冗余” 。

    > [!NOTE]
    > 在提供[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)设置的区域中，可以选择无区域（默认选项）、特定区域或区域冗余。 请根据特定的域故障要求做出选择。 在不提供可用性区域设置的区域中，不会显示此字段。 </br> 有关可用性区域的详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

12. 保留“路由首选项”的默认值“Microsoft 网络” 。

13. 选择“确定” 。

14. 选择“添加”  。

15. 在页面底部选择“下一页: 后端池”。

16. 在“后端池”选项卡上，选择“+ 添加后端池” 。

17. 在“添加后端池”的“名称”中输入“myBackendPool”  。

18. 在“虚拟网络”中，选择“myVNet” 。

19. 对于“后端池配置”，请选择“NIC”或“IP 地址”  。

20. 对于“IP 版本”，请选择“IPv4”或“IPv6”  。

21. 选择 **添加** 。

22. 选择页面底部的“下一页: 入站规则”按钮。

23. 在“入站规则”选项卡的“负载均衡规则”中，选择“+ 添加负载均衡规则”  。

24. 在“添加负载均衡规则”中输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入“myHTTPRule” |
    | IP 版本 | 根据要求选择“IPv4”或“IPv6” 。 |
    | 前端 IP 地址 | 选择“LoadBalancerFrontEnd”。 |
    | 协议 | 选择“TCP”。 |
    | 端口 | 输入 **80**。 |
    | 后端端口 | 输入 **80**。 |
    | 后端池 | 选择“myBackendPool”。 |
    | 运行状况探测 | 选择“新建”。 </br> 在“名称”中输入“myHealthProbe” 。 </br> 在“协议”中选择“HTTP” 。 </br> 将剩余的字段保留为默认值，然后选择“确定”。 |
    | 会话暂留 | 选择“无”。 |
    | 空闲超时（分钟） | 输入或选择“15”。 |
    | TCP 重置 | 选择“启用”。  |
    | 浮动 IP | 选择“已禁用”。  |
    | 出站源网络地址转换 (SNAT) | 保留默认值“(建议)使用出站规则为后端池成员提供对 Internet 的访问权限。” |

25. 选择 **添加** 。

26. 选择页面底部的“查看 + 创建”蓝色按钮。

27. 选择“创建”。

    > [!NOTE]
    > 本示例创建了一个 NAT 网关来提供出站 Internet 访问。 绕过了配置中的出站规则选项卡，因为它是可选选项，且 NAT 网关不需要它。 有关 Azure NAT 网关的详细信息，请参阅[什么是虚拟网络 NAT？](../virtual-network/nat-gateway/nat-overview.md)
    > 有关 Azure 中出站连接的详细信息，请参阅[出站连接的源网络地址转换 (SNAT)](../load-balancer/load-balancer-outbound-connections.md)

## <a name="create-virtual-machines"></a>创建虚拟机

本部分将创建两个可用性组，每个组包含两个虚拟机。 在创建过程中，这些计算机将添加到负载均衡器的后端池中。 

### <a name="create-first-set-of-vms"></a>创建第一组 VM

1. 在门户的左上角选择“+ 创建资源”。

2. 在“新建”中，选择“计算” > “虚拟机”  。

3. 在“创建虚拟机”的“基本信息”选项卡中，输入或选择以下信息 ：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅 |
    | 资源组 | 选择 TutorLBmultiAVS-rg。 |
    | **实例详细信息** |   |
    | 虚拟机名称 | 输入“myVM1”。 |
    | 区域 | 选择“(US) 美国西部 2”。 |
    | 可用性选项 | 选择“可用性集”。 |
    | 可用性集 | 选择“新建”。 </br> 在“名称”中输入 myAvailabilitySet1 。 </br> 选择“确定”。 |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen1”。 |
    | Azure Spot 实例 | 保留默认值“未选中”。 |
    | 大小 | 选择虚拟机的大小。 |
    | **管理员帐户** |   |
    | 用户名 | 输入用户名。 |
    | Password | 输入密码。 |
    | **入站端口规则** |   |
    | 公共入站端口 | 选择“无”。 |

4. 选择“网络”选项卡，或者选择“下一页:网络”按钮（位于页面底部）  。

5. 在“网络”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **网络接口** |   |
    | 虚拟网络 | 选择“myVNet”。 |
    | 子网 | 选择“myBackendSubnet”。 |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | 选择“高级”。 |
    | 配置网络安全组 | 选择“新建”。 </br> 在“名称”中，输入 myNSG 。 </br> 在“入站规则”中，选择“+ 添加入站规则” 。 </br> 在“服务”中，选择“HTTP” 。 </br> 在“名称”中输入 myHTTPrule 。 </br> 选择 **添加** 。 </br> 选择“确定”。 | 
    | **负载均衡** |   |
    | 是否将此虚拟机置于现有的负载均衡解决方案之后？ | 选择此复选框。 |
    | **负载均衡设置** |   |
    | 负载均衡选项 | 选择“Azure 负载均衡器”。 |
    | 选择负载均衡器 | 选择“myLoadBalancer”。 |
    | 选择后端池 | 选择“myBackendPool”。 |

6. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

7. 选择“创建”。

8. 重复步骤 1 到步骤 7，创建第二组虚拟机。 将 VM 的设置替换为以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入“myVM2”。 |
    | 可用性集 | 选择 myAvailabilitySet1。 |
    | 虚拟网络 | 选择“myVNet”。 |
    | 子网 | 选择“myBackendSubnet”。 |
    | 公共 IP | 选择“无”。 |
    | 网络安全组 | 选择 myNSG。 |
    | 负载均衡选项 | 选择“Azure 负载均衡器”。 |
    | 选择负载均衡器 | 选择“myLoadBalancer”。 |
    | 选择后端池 | 选择“myBackendPool”。 |

### <a name="create-second-set-of-vms"></a>创建第二组 VM

1. 在门户的左上角选择“+ 创建资源”。

2. 在“新建”中，选择“计算” > “虚拟机”  。

3. 在“创建虚拟机”的“基本信息”选项卡中，输入或选择以下信息 ：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅 |
    | 资源组 | 选择 TutorLBmultiAVS-rg。 |
    | **实例详细信息** |   |
    | 虚拟机名称 | 输入“myVM3”。 |
    | 区域 | 选择“(US) 美国西部 2”。 |
    | 可用性选项 | 选择“可用性集”。 |
    | 可用性集 | 选择“新建”。 </br> 在“名称”中输入 myAvailabilitySet2 。 </br> 选择“确定”。 |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen1”。 |
    | Azure Spot 实例 | 保留默认值“未选中”。 |
    | 大小 | 选择虚拟机的大小。 |
    | **管理员帐户** |   |
    | 用户名 | 输入用户名。 |
    | Password | 输入密码。 |
    | **入站端口规则** |   |
    | 公共入站端口 | 选择“无”。 |

4. 选择“网络”选项卡，或者选择“下一页:网络”按钮（位于页面底部）  。

5. 在“网络”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **网络接口** |   |
    | 虚拟网络 | 选择“myVNet”。 |
    | 子网 | 选择“myBackendSubnet”。 |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | 选择“高级”。 |
    | 配置网络安全组 | 选择 myNSG。 | 
    | **负载均衡** |   |
    | 是否将此虚拟机置于现有的负载均衡解决方案之后？ | 选择此复选框。 |
    | **负载均衡设置** |   |
    | 负载均衡选项 | 选择“Azure 负载均衡器”。 |
    | 选择负载均衡器 | 选择“myLoadBalancer”。 |
    | 选择后端池 | 选择“myBackendPool”。 |

6. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

7. 选择“创建”。

8. 重复步骤 1 到步骤 7，创建第二组虚拟机。 将 VM 的设置替换为以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入“myVM4”。 |
    | 可用性集 | 选择 myAvailabilitySet2。 |
    | 虚拟网络 | 选择“myVNet”。 |
    | 网络安全组 | 选择 myNSG。 |
    | 负载均衡选项 | 选择“Azure 负载均衡器”。 |
    | 选择负载均衡器 | 选择“myLoadBalancer”。 |
    | 选择后端池 | 选择“myBackendPool”。 |

## <a name="install-iis"></a>安装 IIS

本部分将使用之前创建的 Azure Bastion 主机连接到虚拟机并安装 IIS。

1. 在门户顶部的搜索框中，输入“虚拟机”。

2. 在搜索结果中，选择“虚拟机”。

3. 选择 myVM1。

4. 在 myVM1 的“概述”页上，选择“连接” > “Bastion”  。

5. 选择“使用 Bastion”。

6. 输入创建 VM 时指定的用户名和密码 。

7. 选择“连接”。

7. 在服务器桌面上，导航到“Windows 管理工具” > “Windows PowerShell”。

8. 在 PowerShell 窗口中，运行以下命令以：

    * 安装 IIS 服务器
    * 删除默认的 iisstart.htm 文件
    * 添加显示 VM 名称的新 iisstart.htm 文件：

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. 关闭与 myVM1 之间的 Bastion 会话。

9. 对于 myVM2、myVM3 和 myVM4，重复步骤 1 到步骤 8  。

## <a name="test-the-load-balancer"></a>测试负载均衡器

本部分将发现负载均衡器的公共 IP 地址。 将使用 IP 地址测试负载均衡器的操作。

1. 在门户顶部的搜索框中，输入“公共 IP”。

2. 在搜索结果中，选择“公共 IP 地址”。

3. 选择“myPublicIP-lb”。

4. 查看 myPublicIP-lb 的“概述”页“IP 地址”中列出的公共 IP 地址  ：

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/find-public-ip.png" alt-text="查找负载均衡器的公共 IP 地址。" border="true":::

5. 打开 web 浏览器，在地址栏中输入公共 IP 地址：

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/verify-load-balancer.png" alt-text="在 web 浏览器中测试负载均衡器。" border="true":::

6. 在浏览器中选择“刷新”，查看流量是否均衡分布到后端池中的其他虚拟机。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请按以下步骤删除负载均衡器和支持资源：

1. 在门户顶部的搜索框中输入“资源组”。

2. 在搜索结果中选择“资源组”。

3. 选择 TutorLBmultiAVS-rg。

4. 在 TutorLBmultiAVS-rg 的概述页面中，选择“删除资源组” 。

5. 在“键入资源组名称”中，输入 TutorLBmultiAVS-rg 。

6. 选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

* 创建了虚拟网络和 Azure Bastion 主机。
* 创建了 Azure 标准负载均衡器。
* 创建了两个可用性集，每组包含两个虚拟机。
* 安装了 IIS 并测试了负载均衡器。

请继续学习下一篇文章，了解如何创建跨区域 Azure 负载均衡器：
> [!div class="nextstepaction"]
> [创建跨区域负载均衡器](tutorial-cross-region-portal.md)

