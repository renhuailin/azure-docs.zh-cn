---
title: 快速入门：创建内部负载均衡器 - Azure 门户
titleSuffix: Azure Load Balancer
description: 本快速入门介绍如何使用 Azure 门户创建内部负载均衡器。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.date: 08/09/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 9c8f46bf615b0d0c9fe8289057a8244a1d57185d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732784"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建内部负载均衡器以对 VM 进行负载均衡

使用 Azure 门户创建内部负载均衡器和三个虚拟机，通过这种方式开始使用 Azure 负载均衡器。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

---

# <a name="standard-sku"></a>[**标准 SKU**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>对于生产型工作负载，建议使用标准 SKU 负载均衡器。  有关 SKU 的详细信息，请参阅 [Azure 负载均衡器 SKU](skus.md)。

创建内部负载均衡器时，虚拟网络配置为负载均衡器的网络。 

虚拟网络中的专用 IP 地址配置为负载均衡器的前端（默认名称为 LoadBalancerFrontend）。 

前端 IP 地址可以是静态的，也可以是动态的 。

## <a name="create-the-virtual-network"></a>创建虚拟网络

在本部分，请创建虚拟网络和子网。

1. 在门户顶部的搜索框中，输入“虚拟网络”。 在搜索结果中，选择“虚拟网络”。

2. 在“虚拟网络”中，选择“+ 创建” 。

3. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅                                  |
    | 资源组   | 选择“新建”。 </br> 在“名称”中，输入 CreateIntLBQS-rg 。 </br> 选择“确定”。 |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入“myVNet”                                    |
    | 区域           | 选择“(欧洲)西欧” |

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

## <a name="create-nat-gateway"></a>创建 NAT 网关

在此部分中，你将为虚拟网络中的资源创建用于出站 Internet 访问的 NAT 网关。 

1. 在门户顶部的搜索框中，输入“NAT 网关”。 在搜索结果中选择“NAT 网关”。

2. 在“NAT 网关”中，选择“+ 创建” 。

3. 在“创建网络地址转换(NAT)网关”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“CreateIntLBQS-rg”。 |
    | **实例详细信息** |    |
    | NAT 网关名称 | 输入 myNATGateway。 |
    | 可用性区域 | 选择“无”。 |
    | 空闲超时（分钟） | 输入 **15**。 |

4. 选择“出站 IP”选项卡，或者选择“下一步: 出站 IP”按钮（位于页面底部） 。

5. 在“出站 IP”选项卡中，选择“公共 IP 地址”旁边的“创建新的公共 IP 地址”  。

6. 在“添加公共 IP 地址”的“名称”中输入“myNATgatewayIP”  。

7. 选择“确定”。

8. 选择“子网”选项卡，或者选择“下一步: 子网”按钮（位于页面底部） 。

9. 在“子网”选项卡的“虚拟网络”中，选择“myVNet”  。

10. 在“子网名称”下选择“myBackendSubnet”。 

11. 选择页面底部的“查看 + 创建”按钮，或选择“查看 + 创建”选项卡 。

12. 选择“创建”。

## <a name="create-load-balancer"></a><a name="create-load-balancer-resources"></a> 创建负载均衡器

在本部分，你将创建一个负载均衡器来对虚拟机进行负载均衡。

在创建负载均衡器的过程中，你将配置：

* 前端 IP 地址
* 后端池
* 入站负载均衡规则

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 在“负载均衡器”页上，选择“创建” 。

3. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | **项目详细信息** |   |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“CreateIntLBQS-rg”。 |
    | **实例详细信息** |   |
    | 名称                   | 输入“myLoadBalancer”                                   |
    | 区域         | 选择“(欧洲)西欧”。                                        |
    | 类型          | 选择“内部”。                                        |
    | SKU           | 保留默认值“标准”。 |

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="创建标准负载均衡器基本信息选项卡的屏幕截图。" border="true":::

4. 在页面底部选择“下一步: 前端 IP 配置”。

5. 在“前端 IP 配置”中，选择“+ 添加前端 IP” 。

6. 在“名称”中输入“LoadBalancerFrontend” 。

7. 在“子网”中选择“myBackendSubnet” 。

8. 为“分配”选择“动态”。

9. 在“可用性区域”中选择“区域冗余” 。

    > [!NOTE]
    > 在提供 [可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)设置的区域中，可以选择无区域（默认选项）、特定区域或区域冗余。 请根据特定的域故障要求做出选择。 在不提供“可用性区域”设置的区域中，不会显示此字段。 </br> 有关可用性区域的详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

10. 选择 **添加** 。

11. 在页面底部选择“下一步: 后端池”。

12. 在“后端池”选项卡上，选择“+ 添加后端池” 。

13. 在“添加后端池”的“名称”中输入“myBackendPool”  。

14. 对于“后端池配置”，请选择“NIC”或“IP 地址”  。

15. 对于“IP 版本”，请选择“IPv4”或“IPv6”  。

16. 选择 **添加** 。

17. 选择页面底部的“下一步: 入站规则”按钮。

18. 在“入站规则”选项卡的“负载均衡规则”中，选择“+ 添加负载均衡规则”  。

19. 在“添加负载均衡规则”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入“myHTTPRule” |
    | IP 版本 | 根据你的要求选择“IPv4”或“IPv6” 。 |
    | 前端 IP 地址 | 选择“LoadBalancerFrontend”。 |
    | 协议 | 选择“TCP”。 |
    | 端口 | 输入 **80**。 |
    | 后端端口 | 输入 **80**。 |
    | 后端池 | 选择“myBackendPool”。 |
    | 运行状况探测 | 选择“新建”。 </br> 在“名称”中，输入“myHealthProbe” 。 </br> 在“协议”中选择“HTTP” 。 </br> 将剩余的字段保留默认设置，然后选择“确定”。 |
    | 会话暂留 | 选择“无”。 |
    | 空闲超时（分钟） | 输入或选择“15”。 |
    | TCP 重置 | 选择“启用”。  |
    | 浮动 IP | 选择“已禁用”。  |

20. 选择 **添加** 。

21. 选择页面底部的“查看 + 创建”蓝色按钮。

22. 选择“创建”。

    > [!NOTE]
    > 本示例创建了 NAT 网关来提供出站 Internet 访问。 绕过了配置中的出站规则选项卡，NAT 网关不需要它。 有关 Azure NAT 网关的详细信息，请参阅[什么是虚拟网络 NAT？](../virtual-network/nat-gateway/nat-overview.md)
    > 有关 Azure 中出站连接的详细信息，请参阅[出站连接的源网络地址转换 (SNAT)](../load-balancer/load-balancer-outbound-connections.md)

## <a name="create-virtual-machines"></a>创建虚拟机

在本部分中，你将在三个不同的区域（“区域 1”、“区域 2”和“区域 3”）创建三个 VM（“myVM1”、“myVM2”和“myVM3”）     。 

这些 VM 将添加到先前创建的负载均衡器的后端池中。

1. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 在“虚拟机”中，选择“+ 创建” > “虚拟机”。
   
3. 在“创建虚拟机”中，在“基本信息”选项卡中输入或选择值 ：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“CreateIntLBQS-rg” |
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

4. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
5. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | myVNet |
    | 子网 | myBackendSubnet |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | 选择“高级”|
    | 配置网络安全组 | 选择“新建”。 </br> 在“创建网络安全组”中，在“名称”中输入“myNSG”  。 </br> 在“入站规则”下，选择“+ 添加入站规则” 。 </br> 在“目标端口范围”下，输入“80” 。 </br> 在“优先级”下，输入“100” 。 </br> 在“名称”中输入“myNSGRule”  </br> 选择“添加” </br> 选择“确定” |
    | **负载均衡**  |
    | 是否将此虚拟机置于现有的负载均衡解决方案之后？ | 选择相应的框。 |
    | **负载均衡设置** |
    | 负载均衡选项 | 选择“Azure 负载均衡” |
    | 选择负载均衡器 | 选择“myLoadBalancer”  |
    | 选择后端池 | 选择“myBackendPool” |
   
6. 选择“查看 + 创建”。 
  
7. 检查设置，然后选择“创建”。

8. 按照步骤 1 到 7 操作，使用以下值创建另外两个 VM，所有其他设置均与 myVM1 相同：

    | 设置 | VM 2| VM 3|
    | ------- | ----- |---|
    | 名称 |  **myVM2** |**myVM3**|
    | 可用性区域 | **2** |**3**|
    | 网络安全组 | 选择现有的“myNSG”| 选择现有的“myNSG”|

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-2-create-load-balancer-basic)

>[!NOTE]
>对于生产型工作负载，建议使用标准 SKU 负载均衡器。  有关 SKU 的详细信息，请参阅 [Azure 负载均衡器 SKU](skus.md)。

## <a name="create-the-virtual-network"></a>创建虚拟网络

在本部分，请创建虚拟网络和子网。

1. 在门户顶部的搜索框中，输入“虚拟网络”。 在搜索结果中，选择“虚拟网络”。

2. 在“虚拟网络”中，选择“+ 创建” 。

3. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅                                  |
    | 资源组   | 选择“新建”。 </br> 在“名称”中，输入 CreateIntLBQS-rg 。 </br> 选择“确定”。 |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入“myVNet”                                    |
    | 区域           | 选择“(欧洲)西欧” |

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
    | 子网地址范围 | 输入 10.1.0.0/27 |

8. 选择“保存” 。

9. 选择“安全”选项卡。

10. 在“BastionHost”下，选择“启用” 。 输入此信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名称 | 输入“myBastionHost” |
    | AzureBastionSubnet 地址空间 | 输入“10.1.1.0/24” |
    | 公共 IP 地址 | 选择“新建”。 </br> 对于“名称”，请输入“myBastionIP” 。 </br> 选择“确定”。 |

11. 选择“查看 + 创建”选项卡，或选择“查看 + 创建”按钮。

12. 选择“创建”。

## <a name="create-load-balancer"></a>创建负载均衡器

在本部分，你将创建一个负载均衡器来对虚拟机进行负载均衡。

在创建负载均衡器的过程中，你将配置：

* 前端 IP 地址
* 后端池
* 入站负载均衡规则

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 在“负载均衡器”页上，选择“创建” 。

3. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | **项目详细信息** |   |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“CreateIntLBQS-rg”。 |
    | **实例详细信息** |   |
    | 名称                   | 输入“myLoadBalancer”                                   |
    | 区域         | 选择“(欧洲)西欧”。                                        |
    | 类型          | 选择“内部”。                                        |
    | SKU           | 选择“基本”。 |

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="创建标准负载均衡器基本信息选项卡的屏幕截图。" border="true":::

4. 在页面底部选择“下一步: 前端 IP 配置”。

5. 在“前端 IP 配置”中，选择“+ 添加前端 IP” 。

6. 在“名称”中输入“LoadBalancerFrontend” 。

7. 在“子网”中选择“myBackendSubnet” 。

8. 为“分配”选择“动态”。

9. 选择 **添加** 。

10. 在页面底部选择“下一步: 后端池”。

11. 在“后端池”选项卡上，选择“+ 添加后端池” 。

12. 在“添加后端池”的“名称”中输入“myBackendPool”  。

13. 在“关联到”下，选择“虚拟机” 。

14. 对于“IP 版本”，请选择“IPv4”或“IPv6”  。

15. 选择 **添加** 。

16. 选择页面底部的“下一步: 入站规则”按钮。

17. 在“入站规则”选项卡的“负载均衡规则”中，选择“+ 添加负载均衡规则”  。

18. 在“添加负载均衡规则”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入“myHTTPRule” |
    | IP 版本 | 根据你的要求选择“IPv4”或“IPv6” 。 |
    | 前端 IP 地址 | 选择“LoadBalancerFrontend”。 |
    | 协议 | 选择“TCP”。 |
    | 端口 | 输入 **80**。 |
    | 后端端口 | 输入 **80**。 |
    | 后端池 | 选择“myBackendPool”。 |
    | 运行状况探测 | 选择“新建”。 </br> 在“名称”中，输入“myHealthProbe” 。 </br> 在“协议”中选择“HTTP” 。 </br> 将剩余的字段保留默认设置，然后选择“确定”。 |
    | 会话暂留 | 选择“无”。 |
    | 空闲超时（分钟） | 输入或选择“15”。 |
    | 浮动 IP | 选择“已禁用”。  |

19. 选择 **添加** 。

20. 选择页面底部的“查看 + 创建”蓝色按钮。

21. 选择“创建”。

    > [!NOTE]
    > 本示例创建了 NAT 网关来提供出站 Internet 访问。 绕过了配置中的出站规则选项卡，NAT 网关不需要它。 有关 Azure NAT 网关的详细信息，请参阅[什么是虚拟网络 NAT？](../virtual-network/nat-gateway/nat-overview.md)
    > 有关 Azure 中出站连接的详细信息，请参阅[出站连接的源网络地址转换 (SNAT)](../load-balancer/load-balancer-outbound-connections.md)

## <a name="create-virtual-machines"></a>创建虚拟机

在本部分，我们将创建三个 VM（myVM1、myVM2 和 myVM3）  。

这三个 VM 将添加到名为“myAvailabilitySet”的可用性集中。

1. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 在“虚拟机”中，选择“+ 创建” > “虚拟机”。
   
3. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“CreateIntLBQS-rg” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM1” |
    | 区域 | 选择“(欧洲)西欧” |
    | 可用性选项 | 选择“可用性集” |
    | 可用性集 | 选择“新建”。 </br> 在“名称”中输入 myAvailabilitySet 。 </br> 选择“确定” |
    | 映像 | **Windows Server 2019 Datacenter - Gen1** |
    | Azure Spot 实例 | 保留默认值“未选中”。 |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |
    | **入站端口规则** |   |
    | 公共入站端口 | 选择“无”。 |

4. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
5. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | 选择 myVNet |
    | 子网 | 选择“myBackendSubnet” |
    | 公共 IP | 选择“无” |
    | NIC 网络安全组 | 选择“高级”|
    | 配置网络安全组 | 选择“新建”。 </br> 在“创建网络安全组”中，在“名称”中输入“myNSG”  。 </br> 在“入站规则”下，选择“+ 添加入站规则” 。 </br> 在“目标端口范围”下，输入“80” 。 </br> 在“优先级”下，输入“100” 。 </br> 在“名称”中输入“myNSGRule”  </br> 选择“添加” </br> 选择“确定” |
    | **负载均衡**  |
    | 是否将此虚拟机置于现有的负载均衡解决方案之后？ | 选择相应的框 |
    | **负载均衡设置** |   |
    | 负载均衡选项 | 选择“Azure 负载均衡器”。 |
    | 选择负载均衡器 | 选择“myLoadBalancer”。 |
    | 选择后端池 | 选择“myBackendPool”。 |
 
6. 选择“查看 + 创建”。 
  
7. 检查设置，然后选择“创建”。

8. 按照步骤 1 到 7 操作，使用以下值创建另外两个 VM，所有其他设置均与 myVM1 相同：

    | 设置 | VM 2 | VM 3 |
    | ------- | ----- |---|
    | 名称 |  **myVM2** |**myVM3**|
    | 可用性集| 选择“myAvailabilitySet” | 选择“myAvailabilitySet”|
    | 网络安全组 | 选择现有的“myNSG”| 选择现有的“myNSG”|

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]
---

## <a name="create-test-virtual-machine"></a>创建测试虚拟机

在本部分中，你将创建一个名为“myTestVM”的 VM。  此 VM 将用于测试负载均衡器配置。

1. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 在“虚拟机”中，选择“+ 创建” > “虚拟机”。
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“CreateIntLBQS-rg” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myTestVM” |
    | 区域 | 选择“(欧洲)西欧” |
    | 可用性选项 | 选择“无需基础结构冗余” |
    | 映像 | 选择“Windows Server 2019 Datacenter” |
    | Azure Spot 实例 | 保留默认值“未选中”。 |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |
    | **入站端口规则** |   |
    | 公共入站端口 | 选择“无”。 |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | myVNet |
    | 子网 | myBackendSubnet |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | 选择“高级” |
    | 配置网络安全组 | 选择在上一步中创建的 MyNSG。|
       
5. 选择“查看 + 创建”。 
  
6. 检查设置，然后选择“创建”。

## <a name="install-iis"></a>安装 IIS

1. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

2. 选择 myVM1。

3. 在“概述”页上，选择“连接”，然后选择“Bastion”  。

4. 选择“使用 Bastion”。 

5. 输入在 VM 创建过程中输入的用户名和密码。

6. 选择“连接”。

7. 在服务器桌面上，导航到“Windows 管理工具” > “Windows PowerShell” > “Windows PowerShell”。

8. 在 PowerShell 窗口中执行以下命令，以便：

    * 安装 IIS 服务器。
    * 删除默认的 iisstart.htm 文件。
    * 添加显示 VM 名称的新 iisstart.htm 文件。

   ```powershell
    
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```

9. 关闭与 myVM1 之间的 Bastion 会话。

10. 重复步骤 1 到步骤 9，在 myVM2 和 myVM3 上安装 IIS 和更新后的 iisstart.htm 文件 。

## <a name="test-the-load-balancer"></a>测试负载均衡器

在此部分中，你将通过连接到 myTestVM 并验证网页来测试负载均衡器。

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 选择“myLoadBalancer”。

3. 在 myLoadBalancer 的“概览”中，记下或复制专用 IP 地址旁边的地址  。

4. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

5. 选择“myTestVM”。

6. 在“概述”页上，选择“连接”，然后选择“Bastion”  。

7. 选择“使用 Bastion”。

8. 输入在 VM 创建过程中输入的用户名和密码。

9. 在 myTestVM 中打开 Internet Explorer 。

10. 将上一步骤的 IP 地址输入到浏览器的地址栏。 显示后端服务器名称之一的自定义页会显示在浏览器中。

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="屏幕截图显示了按预期显示自定义页的浏览器窗口。" border="true":::
   
若要查看负载均衡器如何在两个 VM 之间分配流量，可以从客户端计算机强制刷新 Web 浏览器。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、负载均衡器以及所有相关资源，请将其删除。 为此，请选择包含资源的资源组“CreateIntLBQS-rg”，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本快速入门中，请执行以下操作：

* 已创建 Azure 标准或基本内部负载均衡器
* 已将 3 个 VM 连接到负载均衡器。
* 已配置负载均衡器流量规则、运行状况探测器，然后测试负载均衡器。 

若要详细了解 Azure 负载均衡器，请继续学习：
> [!div class="nextstepaction"]
> [什么是 Azure 负载均衡器？](load-balancer-overview.md)