---
title: 仅出站的负载均衡器配置
titleSuffix: Azure Load Balancer
description: 在本文中，了解如何使用出站 NAT 创建内部负载均衡器
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/21/2021
ms.author: allensu
ms.openlocfilehash: d2f54b8ea7161f297528ac6ea82fde548827033a
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681185"
---
# <a name="outbound-only-load-balancer-configuration"></a>仅出站的负载均衡器配置

使用内部和外部标准负载均衡器的组合为内部负载均衡器后面的 VM 创建出站连接。 

此配置为内部负载均衡器方案提供出站 NAT，为后端池生成“仅出口”设置。

> [!NOTE]
> 对于生产部署中出站连接，推荐的配置是 **Azure 虚拟网络 NAT**。 有关 **Azure 虚拟网络 NAT** 和 **NAT 网关** 资源的详细信息，请参阅 **什么是 Azure 虚拟网络 NAT？ [](../virtual-network/nat-gateway/nat-overview.md)** 。
>
> 若要使用 Azure 虚拟网络 NAT 和 NAT 网关部署仅出站负载均衡器配置，请参阅[教程：将 NAT 网关与内部负载均衡器集成 - Azure 门户](../virtual-network/nat-gateway/tutorial-nat-gateway-load-balancer-internal-portal.md)。
>
> 有关 Azure 中出站连接和默认出站访问权限的详细信息，请参阅[出站连接的源网络地址转换 (SNAT)](load-balancer-outbound-connections.md)和[默认出站访问权限](../virtual-network/default-outbound-access.md)。

:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="该图描绘了仅有出口的负载均衡器配置" border="true":::

*图：“仅出口”负载均衡器配置*

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-virtual-network-and-load-balancers"></a>创建虚拟网络和负载均衡器

在本节中，将为负载均衡器和虚拟机创建一个虚拟网络和子网。  接下来将创建负载均衡器。

### <a name="create-the-virtual-network"></a>创建虚拟网络

在本节中，将为虚拟机、负载平衡器和堡垒主机创建虚拟网络和子网。

1. 在门户顶部的搜索框中，输入“虚拟网络”。 在搜索结果中，选择“虚拟网络”。

2. 在“虚拟网络”中，选择“+ 创建” 。

3. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅                                  |
    | 资源组   | 选择“新建”。 </br> 在“名称”中输入“myResourceGroupLB”。  </br> 选择“确定”。 |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入“myVNet”                                    |
    | 区域           | 选择“(US)美国东部 2” |

4. 选择“IP 地址”选项卡  ，或选择页面底部的“下一步:  IP 地址”按钮。

5. 在“IP 地址”  选项卡上，输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | IPv4 地址空间 | 输入“10.1.0.0/16” |

6. 在“子网名称”下，选择词语“默认”。

7. 在“编辑子网”中输入以下信息： 

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | 子网名称 | 输入“myBackendSubnet” |
    | 子网地址范围 | 输入“10.1.0.0/24” |

8. 选择“保存” 。

9. 选择“安全”选项卡。

10. 在“BastionHost”下，选择“启用” 。 输入此信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名称 | 输入“myBastionHost” |
    | AzureBastionSubnet 地址空间 | 输入 **10.1.1.0/27** |
    | 公共 IP 地址 | 选择“新建”。 </br> 对于“名称”，请输入“myBastionIP” 。 </br> 选择“确定”。 |

11. 选择“查看 + 创建”选项卡，或选择“查看 + 创建”按钮。

12. 选择“创建”。

### <a name="create-internal-load-balancer"></a>创建内部负载均衡器

在本节中，将创建内部负载平衡器。

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 在“负载均衡器”页上，选择“创建” 。

3. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | **项目详细信息** |   |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“myResourceGroupLB”。 |
    | **实例详细信息** |   |
    | 名称                   | 输入“myInternalLoadBalancer”                                   |
    | 区域         | 选择“(US)美国东部 2”。                                        |
    | 类型          | 选择“内部”。                                        |
    | SKU           | 保留默认值“标准”。 |

4. 在页面底部选择“下一步: 前端 IP 配置”。

5. 在“前端 IP 配置”中，选择“+ 添加前端 IP” 。

6. 在“名称”中输入“LoadBalancerFrontEnd” 。

7. 在“子网”中选择“myBackendSubnet” 。

8. 为“分配”选择“动态”。

9. 在“可用性区域”中选择“区域冗余” 。

    > [!NOTE]
    > 在提供[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) 设置的区域中，可以选择无区域（默认选项）、特定区域或区域冗余。 请根据特定的域故障要求做出选择。 在不提供“可用性区域”设置的区域中，不会显示此字段。 </br> 有关可用性区域的详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

10. 选择 **添加** 。

11. 在页面底部选择“下一步: 后端池”。

12. 在“后端池”选项卡上，选择“+ 添加后端池” 。

13. 在“添加后端池”的“名称”中输入“myInternalBackendPool”  。

14. 对于“后端池配置”，请选择“NIC”或“IP 地址”  。

15. 对于“IP 版本”，请选择“IPv4”或“IPv6”  。

16. 选择 **添加** 。

17. 选择页面底部的“查看 + 创建”蓝色按钮。

18. 选择“创建”。

### <a name="create-public-load-balancer"></a>创建公共负载均衡器

在本节中，将创建公共负载平衡器。

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 在“负载均衡器”页上，选择“创建” 。

3. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | **项目详细信息** |   |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“myResourceGroupLB”。 |
    | **实例详细信息** |   |
    | 名称                   | 输入“myPublicLoadBalancer”                                   |
    | 区域         | 选择“(US)美国东部 2”。                                        |
    | 类型          | 选择“公共”。                                        |
    | SKU           | 保留默认值“标准”。 |
    | 层          | 保留默认值“区域”。 |

4. 在页面底部选择“下一步: 前端 IP 配置”。

5. 在“前端 IP 配置”中，选择“+ 添加前端 IP” 。

6. 在“名称”中输入“LoadBalancerFrontEnd” 。

7. 对于“IP 版本”，选择“IPv4”或“IPv6”  。

    > [!NOTE]
    > IPv6 目前不支持路由首选项或跨区域负载平衡（全局层）。

8. 对于“IP 类型”，选择“IP 地址” 。

    > [!NOTE]
    > 有关 IP 前缀的详细信息，请参阅 [Azure 公共 IP 地址前缀](../virtual-network/public-ip-address-prefix.md)。

9. 在“公共 IP 地址”中选择“新建” 。

10. 在“添加公共 IP 地址”的“名称”中，输入“myPublicIP”  。

11. 在“可用性区域”中选择“区域冗余” 。

    > [!NOTE]
    > 在提供[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) 设置的区域中，可以选择无区域（默认选项）、特定区域或区域冗余。 请根据特定的域故障要求做出选择。 在不提供“可用性区域”设置的区域中，不会显示此字段。 </br> 有关可用性区域的详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

12. 保留“路由首选项”的默认值“Microsoft 网络” 。

13. 选择“确定” 。

14. 选择“添加”  。

15. 在页面底部选择“下一步: 后端池”。

16. 在“后端池”选项卡上，选择“+ 添加后端池” 。

17. 在“添加后端池”的“名称”中输入“myPublicBackendPool”  。

18. 在“虚拟网络”中，选择“myVNet” 。

19. 对于“后端池配置”，请选择“NIC”或“IP 地址”  。

20. 对于“IP 版本”，请选择“IPv4”或“IPv6”  。

21. 选择 **添加** 。

22. 选择页面底部的“查看 + 创建”蓝色按钮。

23. 选择“创建”。

## <a name="create-virtual-machine"></a>创建虚拟机

在本节中，将创建虚拟机。 创建期间，需要将其添加到内部负载均衡器的后端池。 创建虚拟机后，将虚拟机添加到公共负载均衡器的后端池。

1. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 在“虚拟机”中，选择“+ 创建” > “虚拟机”。
   
3. 在“创建虚拟机”中，在“基本信息”选项卡中输入或选择值 ：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroupLB” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM” |
    | 区域 | 选择“(US)美国东部 2” |
    | 可用性选项 | 选择“无需基础结构冗余” |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen1” |
    | Azure Spot 实例 | 保留默认值“未选中”。 |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |
    | **入站端口规则** |  |
    | 公共入站端口 | 选择“无” |

4. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
5. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | myVNet |
    | 子网 | myBackendSubnet |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | 选择“高级”|
    | 配置网络安全组 | 保留默认值“基本”。 |
    | **负载均衡**  |
    | 是否将此虚拟机置于现有的负载均衡解决方案之后？ | 选择相应的框。 |
    | **负载均衡设置** |
    | 负载均衡选项 | 选择“Azure 负载均衡” |
    | 选择负载均衡器 | 选择“myInternalLoadBalancer”  |
    | 选择后端池 | 选择“myInternalBackendPool” |
   
6. 选择“查看 + 创建”。 
  
7. 检查设置，然后选择“创建”。

### <a name="add-vm-to-backend-pool-of-public-load-balancer"></a>将 VM 添加到公共负载均衡器的后端池

在本节中，需要将前面创建的虚拟机添加到公共负载均衡器的后端池。

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 选择“myPublicLoadBalancer”。

3. 在“myPublicLoadBalancer”中，选择“设置”中的“后端池”。  

4. 在“后端池”页的“后端池”下选择“myPublicBackendPool”。  

5. 在“myPublicBackendPool”中，选择“虚拟网络”中的“myVNet”。  

6. 在“虚拟机”中，选择蓝色的“+ 添加”按钮。

7. 在“向后端池添加虚拟机”中，选中“myVM”旁边的框。 

8. 选择“添加”  。

9. 选择“保存” 。
## <a name="test-connectivity-before-outbound-rule"></a>在出站规则之前测试连接

1. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 选择“myVM”。

3. 在“概述”页上，选择“连接”，然后选择“Bastion”  。

4. 输入在 VM 创建过程中输入的用户名和密码。

5. 选择“连接”。

6. 打开 Internet Explorer。

7. 在地址栏中输入“https://whatsmyip.org”。

8. 该连接应该会失败。 默认情况下，标准公共负载均衡器[在没有已定义的出站规则的情况下不允许有出站流量](load-balancer-overview.md#securebydefault)。
 
## <a name="create-a-public-load-balancer-outbound-rule"></a>创建公共负载均衡器出站规则

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 选择“myPublicLoadBalancer”。

3. 在“myPublicLoadBalancer”中，选择“设置”中的“出站规则”。  

4. 在“出站规则”中选择“+ 添加”。 

5. 输入或选择以下信息以配置出站规则。

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 myOutboundRule。 |
    | 前端 IP 地址 | 选择“LoadBalancerFrontEnd”。|
    | 协议 | 保留默认值“全部”。 |
    | 空闲超时（分钟） | 将滑块移动到 15 分钟。|
    | TCP 重置 | 选择“启用”。|
    | 后端池 | 选择“myPublicBackendPool”。|
    | **端口分配** |  |
    | 端口分配 | 选择“手动选择出站端口数”。 |
    | 出站端口 |  |
    | 选择依据 | 选择“每个实例的端口数”。 |
    | 每个实例的端口数 | 输入“10000”

6. 选择“添加”   。

## <a name="test-connectivity-after-outbound-rule"></a>在出站规则之后测试连接

1. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 选择“myVM”。

3. 在“概述”页上，选择“连接”，然后选择“Bastion”  。

4. 输入在 VM 创建过程中输入的用户名和密码。

5. 选择“连接”。

6. 打开 Internet Explorer。

7. 在地址栏中输入“https://whatsmyip.org”。

8. 该连接应该会成功。

9. 显示的 IP 地址应该是 myPublicLoadBalancer 的前端 IP 地址。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、负载均衡器、VM 及所有相关资源，请将其删除。 

为此，请选择资源组“myResourceGroupLB”，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本文中，你已通过公共和内部两种负载均衡器的组合创建了一个“仅出口”配置。  

利用此配置，你可以将传入的内部流量负载均衡到后端池，而同时仍阻止任何公共入站连接。

有关 Azure 负载均衡器和 Azure Bastion 的详细信息，请参阅[什么是 Azure 负载均衡器？](load-balancer-overview.md)和[什么是 Azure Bastion？](../bastion/bastion-overview.md)。

