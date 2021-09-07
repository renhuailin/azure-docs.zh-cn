---
title: 教程：对多个 IP 配置进行负载均衡 - Azure 门户
titleSuffix: Azure Load Balancer
description: 本文介绍如何使用 Azure 门户对主要和辅助 NIC 配置进行负载均衡。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/08/2021
ms.custom: template-tutorial
ms.openlocfilehash: 1d84de13820b23b52c09d3333799f45fd342763f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260352"
---
# <a name="tutorial-load-balance-multiple-ip-configurations-using-the-azure-portal"></a>教程：使用 Azure 门户对多个 IP 配置进行负载均衡 

若要托管多个网站，可以使用与虚拟机关联的另一个网络接口。 Azure 负载均衡器支持通过部署负载均衡来支持网站的高可用性。

在本教程中，你将了解如何：

> [!div class="checklist"]
> * 创建和配置虚拟网络、子网和 NAT 网关。
> * 创建两个 Windows 服务器虚拟机
> * 为每个虚拟机创建辅助 NIC 和网络配置
> * 在每个虚拟机上创建两个 Internet Information Server (IIS) 网站
> * 将网站绑定到网络配置
> * 创建和配置 Azure 负载均衡器
> * 测试负载均衡器

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-virtual-network"></a>创建虚拟网络

在本部分中，你将为负载均衡器和虚拟机创建一个虚拟网络。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“虚拟网络”。 在搜索结果中，选择“虚拟网络”。

3. 在“虚拟网络”中，选择“+ 创建” 。

4. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅                                  |
    | 资源组   | 选择“新建”。 </br> 在“名称”中，输入“TutorialLBIP-rg”。 </br> 选择“确定”。 |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入“myVNet”                                    |
    | 区域           | 选择“(欧洲)西欧” |

5. 选择“IP 地址”选项卡  ，或选择页面底部的“下一步:  IP 地址”按钮。

6. 在“IP 地址”  选项卡上，输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | IPv4 地址空间 | 输入“10.1.0.0/16” |

7. 在“子网名称”下，选择词语“默认”。

8. 在“编辑子网”中输入以下信息： 

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | 子网名称 | 输入“myBackendSubnet” |
    | 子网地址范围 | 输入“10.1.0.0/24” |

9. 选择“保存” 。

10. 选择“安全”选项卡。

11. 在“BastionHost”下，选择“启用” 。 输入此信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名称 | 输入“myBastionHost” |
    | AzureBastionSubnet 地址空间 | 输入 **10.1.1.0/27** |
    | 公共 IP 地址 | 选择“新建”。 </br> 对于“名称”，请输入“myBastionIP” 。 </br> 选择“确定”。 |


12. 选择“查看 + 创建”选项卡，或选择“查看 + 创建”按钮。

13. 选择“创建”。

## <a name="create-nat-gateway"></a>创建 NAT 网关

在本部分中，你将为虚拟网络中的资源创建用于出站 Internet 访问的 NAT 网关。 

1. 在门户顶部的搜索框中，输入“NAT 网关”。 在搜索结果中选择“NAT 网关”。

2. 在“NAT 网关”中，选择“+ 创建” 。

3. 在“创建网络地址转换(NAT)网关”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“TutorialLBIP-rg”。 |
    | **实例详细信息** |    |
    | NAT 网关名称 | 输入 myNATGateway。 |
    | 可用性区域 | 选择“无”。 |
    | 空闲超时（分钟） | 输入 **15**。 |

4. 选择“出站 IP”选项卡，或者选择页面底部的“下一步: 出站 IP”按钮 。

5. 在“出站 IP”选项卡中，选择“公共 IP 地址”旁边的“创建新的公共 IP 地址”  。

6. 在“添加公共 IP 地址”的“名称”中，输入“myNATgatewayIP”  。

7. 选择“确定”。

8. 选择“子网”选项卡，或者选择页面底部的“下一步: 子网”按钮 。

9. 在“子网”选项卡的“虚拟网络”中，选择“myVNet”  。

10. 在“子网名称”下选择“myBackendSubnet” 。

11. 选择页面底部的“查看 + 创建”蓝色按钮，或选择“查看 + 创建”选项卡 。

12. 选择“创建”。

## <a name="create-virtual-machines"></a>创建虚拟机

在本部分中，你将创建两个虚拟机来托管 IIS 网站。

1. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 在“虚拟机”中，依次选择“+ 创建”、“+ 虚拟机”。

3. 在“创建虚拟机”中，输入或选择以下信息：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“TutorialLBIP-rg” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM1” |
    | 区域 | 选择“(欧洲)西欧” |
    | 可用性选项 | 选择“可用性区域” |
    | 可用性区域 | 选择“1” |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen1” |
    | Azure Spot 实例 | 保留默认值“未选中”。 |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |
    | **入站端口规则** |  |
    | 公共入站端口 | 选择“无” |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | myVNet |
    | 子网 | myBackendSubnet |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | 选择“高级”|
    | 配置网络安全组 | 选择“新建”。 </br> 在“创建网络安全组”中，在“名称”中输入“myNSG”  。 </br> 在“入站规则”下，选择“+ 添加入站规则” 。 </br> 在“服务”下，选择“HTTP” 。 </br> 在“优先级”下，输入“100” 。 </br> 在“名称”中，输入“myNSGrule”  </br> 选择“添加” </br> 选择“确定” |
   
7. 选择“查看 + 创建”  。 
  
8. 检查设置，然后选择“创建”。

9. 按照步骤 1 到 8 操作，使用以下值创建另一个 VM，所有其他设置均与 myVM1 相同：

    | 设置 | VM 2 |
    | ------- | ---- |
    | 名称 |  **myVM2** |
    | 可用性区域 | **2** |
    | 网络安全组 | 选择现有的“myNSG” |

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-secondary-network-configurations"></a>创建辅助网络配置

在本部分中，你要将每个虚拟机的现有 NIC 的专用 IP 地址更改为“静态”。 接着，你将向每个虚拟机添加具有“静态”专用 IP 地址配置的新 NIC 资源。

1. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 选择 myVM1。

3. 如果虚拟机正在运行，请停止虚拟机。 

4. 在“设置”中选择“网络” 。

5. 在“网络”中，选择“网络接口”旁边的网络接口名称 。 该网络接口将以 VM 的名称开头，并分配有一个随机数。 在本示例中，它为 myVM1266。

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-nic.png" alt-text="Azure 门户中 myVM1 网络配置的屏幕截图。":::

6. 在网络接口页上的“设置”中，选择“IP 配置” 。

7. 在“IP 配置”中，选择“ipconfig1” 。

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-ipconfig1.png" alt-text="myVM1 网络接口配置的屏幕截图。":::

8. 在 ipconfig1 配置的“分配”中选择  “静态”。

9. 选择“保存”  。

10. 返回到 myVM1 的“概述”页。

11. 在“设置”中选择“网络” 。

12. 在“网络”页中，选择“附加网络接口”。

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-attach-nic.png" alt-text="myVM1 附加网络接口的屏幕截图。":::

13. 在“附加网络接口”中，选择“创建并附加网络接口” 。

14. 在“创建网络接口”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 资源组 | 选择“TutorialLBIP-rg”。 |
    | **网络接口** |  |
    | 名称 | 输入“myVM1NIC2” |
    | 子网 | 选择“myBackendSubnet (10.1.0.0/24)”。 |
    | NIC 网络安全组 | 选择“高级”。 |
    | 配置网络安全组 | 选择 myNSG。 |
    | 专用 IP 地址分配 | 选择“静态”。 |
    | 专用 IP 地址 | 输入 10.1.0.6。 |

15. 选择“创建”。 

16. 启动虚拟机。

17. 对 myVM2 重复步骤 1 到 16，并替换以下信息：

    | 设置 | myVM2 |
    | ------  | ----- |
    | 名称 | myVM2NIC2 |
    | 专用 IP 地址 | 10.1.0.7 |

## <a name="configure-virtual-machines"></a>配置虚拟机

在本部分中，你将使用 Azure Bastion 连接到 myVM1 和 myVM2，并配置辅助网络配置。 你将为辅助网络配置的网关添加一个路由。 然后，你将在每个虚拟机上安装 IIS，并自定义网站以显示虚拟机的主机名。

1. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 选择 myVM1。

3. 启动 myVM1。

4. 在“概述”中，依次选择“连接”、“Bastion”  。

5. 选择“使用 Bastion”。

6. 输入创建虚拟机时输入的用户名和密码。

7. 选择“允许”让 Bastion 使用剪贴板。

8. 在服务器桌面上，导航到“开始”>“Windows 管理工具”>“Windows PowerShell”>“Windows PowerShell”。

9. 在 PowerShell 窗口中执行 `route print` 命令，此命令将返回类似于以下虚拟机输出的输出，该虚拟机包含两个附加的网络接口：

    ```console
    ===========================================================================
    Interface List
      6...00 22 48 86 00 53 ......Microsoft Hyper-V Network Adapter #2
     13...00 22 48 83 0b da ......Microsoft Hyper-V Network Adapter #3
      1...........................Software Loopback Interface 1
    ===========================================================================
    ```
    在本示例中，Microsoft Hyper-V 网络适配器 #3（接口 13）是辅助网络接口，系统不会向其分配默认网关。

10. 在 PowerShell 窗口中执行 `ipconfig /all` 命令，以查看分配给辅助网络接口的 IP 地址。 在本示例中，10.1.0.6 被分配到接口 13。 辅助网络接口没有返回任何默认网关地址。

11. 若要将子网外地址的所有流量路由到网关，请执行以下命令：

    ```console
    route -p add 0.0.0.0 MASK 0.0.0.0 10.1.0.1 METRIC 5015 IF 13
    ```

    在本示例中，10.1.0.1 是之前创建的虚拟网络的默认网关。

12. 在 PowerShell 窗口中执行以下命令，以安装并配置 IIS 和测试网站：

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.4:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.4:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2 -Force

    ## Remove the default htm file. ##
    Remove-Item c:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

     ## Create a new website and site binding for the second IP address 10.1.0.6. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.6:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)

    }
    Add-Content @para6

    ```
13. 关闭 Bastion 与 myVM1 的连接。

14. 对 myVM2 重复步骤 1 到 13。 使用下面的 PowerShell 代码为 myVM2 安装 IIS。

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.5:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.5:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2

    ## Remove the default htm file. ##
    Remove-Item C:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

    ## Create a new website and site binding for the second IP address 10.1.0.7. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.7:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)
    }
    Add-Content @para6

    ```

## <a name="create-load-balancer"></a>创建负载均衡器

在本部分中，你将创建一个对虚拟机进行负载均衡的区域冗余负载均衡器。 

使用区域冗余时，一个或多个可用性区域可能会发生故障，而数据路径可以幸存，但前提是该区域中有一个局部区域保持正常。

在创建负载均衡器的过程中，你将配置：

* 两个前端 IP 地址，每个网站一个。
* 后端池
* 入站负载均衡规则

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 在“负载均衡器”页上，选择“创建” 。

3. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | **项目详细信息** |   |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“TutorialLBIP-rg”。 |
    | **实例详细信息** |   |
    | 名称                   | 输入“myLoadBalancer”                                   |
    | 区域         | 选择“(欧洲)西欧”。                                        |
    | 类型          | 选择“公共”。                                        |
    | SKU           | 保留默认值“标准”。 |
    | 层          | 保留默认值“区域”。 |

4. 选择页面底部的“下一步: 前端 IP 配置”。

5. 在“前端 IP 配置”中，选择“+ 添加前端 IP” 。

6. 在“名称”中，输入“Frontend-contoso”。

7. 对于“IP 版本”，选择“IPv4” 。

    > [!NOTE]
    > IPv6 目前不支持路由首选项或跨区域负载平衡（全局层）。

8. 对于“IP 类型”，选择“IP 地址” 。

    > [!NOTE]
    > 有关 IP 前缀的详细信息，请参阅 [Azure 公共 IP 地址前缀](../virtual-network/public-ip-address-prefix.md)。

9. 在“公共 IP 地址”中选择“新建” 。

10. 在“添加公共 IP 地址”的“名称”中，输入“myPublicIP-contoso”  。

11. 在“可用性区域”中选择“区域冗余” 。

    > [!NOTE]
    > 在提供[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)设置的区域中，可以选择无区域（默认选项）、特定区域或区域冗余。 请根据特定的域故障要求做出选择。 在不提供“可用性区域”设置的区域中，不会显示此字段。 </br> 有关可用性区域的详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

12. 保留“路由首选项”的默认值“Microsoft 网络” 。

13. 选择“确定” 。

14. 选择“添加”  。

14. 选择“+ 添加前端 IP”。

15. 在“名称”中，输入“Frontend-fabrikam”。

7. 对于“IP 版本”，选择“IPv4” 。

8. 对于“IP 类型”，选择“IP 地址” 。

9. 在“公共 IP 地址”中选择“新建” 。

10. 在“添加公共 IP 地址”的“名称”中，输入“myPublicIP-fabrikam”  。

11. 在“可用性区域”中选择“区域冗余” 。

14. 选择 **添加** 。

15. 选择页面底部的“下一步: 后端池”。

16. 在“后端池”选项卡上，选择“+ 添加后端池” 。

17. 在“添加后端池”的“名称”中，输入“myBackendPool-contoso”  。

18. 在“虚拟网络”中，选择“myVNet” 。

19. 对于“后端池配置”，选择“NIC” 。

20. 对于“IP 版本”，选择“IPv4” 。

21. 在“虚拟机”中，选择“+ 添加”。

22. 选择与 ipconfig1 (10.1.0.4) 和 ipconfig1 (10.1.0.5) 对应的 myVM1 和 myVM2   。

23. 选择 **添加** 。

21. 选择 **添加** 。

22. 选择“+ 添加后端池”。

23. 在“添加后端池”的“名称”中，输入“myBackendPool-fabrikam”  。

24. 在“虚拟网络”中，选择“myVNet” 。

19. 对于“后端池配置”，选择“NIC” 。

20. 对于“IP 版本”，选择“IPv4” 。

21. 在“虚拟机”中，选择“+ 添加”。

22. 选择与 ipconfig1 (10.1.0.6) 和 ipconfig1 (10.1.0.7) 对应的 myVM1 和 myVM2   。

23. 选择 **添加** 。

21. 选择 **添加** 。

22. 选择页面底部的“下一步: 入站规则”按钮。

23. 在“入站规则”选项卡的“负载均衡规则”中，选择“+ 添加负载均衡规则”  。

24. 在“添加负载均衡规则”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入“myHTTPRule-contoso” |
    | IP 版本 | 选择“IPv4”。 |
    | 前端 IP 地址 | 选择“Frontend-contoso”。 |
    | 协议 | 选择“TCP”。 |
    | 端口 | 输入 **80**。 |
    | 后端端口 | 输入 **80**。 |
    | 后端池 | 选择“myBackendPool-contoso”。 |
    | 运行状况探测 | 选择“新建”。 </br> 在“名称”中，输入“myHealthProbe-contoso” 。 </br> 在“协议”中选择“HTTP” 。 </br> 将剩余的字段保留默认设置，然后选择“确定”。 |
    | 会话暂留 | 选择“无”。 |
    | 空闲超时（分钟） | 输入或选择 15。 |
    | TCP 重置 | 选择“启用”。  |
    | 浮动 IP | 选择“已禁用”。  |
    | 出站源网络地址转换 (SNAT) | 保留默认值“(建议)使用出站规则为后端池成员提供对 Internet 的访问权限。” |

25. 选择 **添加** 。

26. 选择“+ 添加负载均衡规则”。

27. 在“添加负载均衡规则”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入“myHTTPRule-fabrikam” |
    | IP 版本 | 选择“IPv4”。 |
    | 前端 IP 地址 | 选择“Frontend-fabrikam”。 |
    | 协议 | 选择“TCP”。 |
    | 端口 | 输入 **80**。 |
    | 后端端口 | 输入 **80**。 |
    | 后端池 | 选择“myBackendPool-fabrikam”。 |
    | 运行状况探测 | 选择“新建”。 </br> 在“名称”中，输入“myHealthProbe-fabrikam” 。 </br> 在“协议”中选择“HTTP” 。 </br> 将剩余的字段保留默认设置，然后选择“确定”。 |
    | 会话暂留 | 选择“无”。 |
    | 空闲超时（分钟） | 输入或选择 15。 |
    | TCP 重置 | 选择“启用”。  |
    | 浮动 IP | 选择“已禁用”。  |
    | 出站源网络地址转换 (SNAT) | 保留默认值“(建议)使用出站规则为后端池成员提供对 Internet 的访问权限。” |

25. 选择 **添加** 。

26. 选择页面底部的“查看 + 创建”蓝色按钮。

27. 选择“创建”。

    > [!NOTE]
    > 在本示例中，我们创建了 NAT 网关来提供出站 Internet 访问。 我们绕过了配置中的出站规则选项卡，因为它是可选选项，且 NAT 网关不需要它。 有关 Azure NAT 网关的详细信息，请参阅[什么是 Azure 虚拟网络 NAT？](../virtual-network/nat-gateway/nat-overview.md)
    > 有关 Azure 中出站连接的详细信息，请参阅[出站连接的源网络地址转换 (SNAT)](../load-balancer/load-balancer-outbound-connections.md)

## <a name="test-load-balancer"></a>测试负载均衡器

在本部分中，你将发现每个网站的公共 IP 地址。 你将在浏览器中输入 IP，以测试之前创建的网站。

1. 在门户顶部的搜索框中，输入“公共 IP”。 在搜索结果中，选择“公共 IP 地址”。

2. 选择“myPublicIP-contoso”。

3. 复制 myPublicIP-contoso 的概述页中的“IP 地址”。

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-contoso.png" alt-text="myPublicIP-fabrikam 公共 IP 地址的屏幕截图。":::

4. 打开 Web 浏览器并将公共 IP 地址粘贴到地址栏中。

    :::image type="content" source="./media/load-balancer-multiple-ip/test-contoso.png" alt-text="Web 浏览器中 contoso 网站的屏幕截图。":::

5. 返回到“公共 IP 地址”。 选择“myPublicIP-fabrikam”。

6. 复制 myPublicIP-fabrikam 的概述页中的“IP 地址”。

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-fabrikam.png" alt-text="myPublicIP-contoso 公共 IP 地址的屏幕截图。":::

7. 打开 Web 浏览器并将公共 IP 地址粘贴到地址栏中。

    :::image type="content" source="./media/load-balancer-multiple-ip/test-fabrikam.png" alt-text="Web 浏览器中 fabrikam 网站的屏幕截图。":::

8. 若要测试负载均衡器，请刷新浏览器或关闭其中一个虚拟机。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除虚拟机和负载均衡器：

1. 在门户顶部的搜索框中输入“资源组”。  在搜索结果中选择“资源组”。

2. 在“资源组”中选择“TutorialLBIP-rg”。

3. 选择“删除资源组”  。

4. 在“键入资源组名称:”中输入“TutorialLBIP-rg” 。 选择“删除”。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何创建跨区域负载均衡器：

> [!div class="nextstepaction"]
> [使用 Azure 门户创建跨区域负载均衡器](tutorial-cross-region-portal.md)
