---
title: 教程：使用 Azure 门户创建跨区域负载均衡器
titleSuffix: Azure Load Balancer
description: 本教程入门：使用 Azure 门户部署跨区域 Azure 负载均衡器。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/02/2021
ms.openlocfilehash: f0004845033493dc7546bb3af467918ea77ebcad
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201610"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>教程：使用 Azure 门户创建跨区域 Azure 负载均衡器

跨区域负载均衡器可确保服务在多个 Azure 区域中全局可用。 如果一个区域出现故障，则会将流量路由到下一个最近的正常运行的区域负载均衡器。  

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建跨区域负载均衡器。
> * 创建包含两个区域负载均衡器的后端池。
> * 创建负载均衡器规则。
> * 测试负载均衡器。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> 跨区域的 Azure 负载均衡器目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。
- 两个标准 SKU Azure 负载均衡器，其后端池部署在两个不同的 Azure 区域中。
    - 有关为后端池创建区域标准负载均衡器和虚拟机的信息，请参阅[快速入门：使用 Azure 门户创建公共负载均衡器，以便对 VM 进行负载均衡](quickstart-load-balancer-standard-public-portal.md)。
        - 在包含 -R1 和 -R2 的每个区域中追加负载均衡器、虚拟机和其他资源的名称 。 

## <a name="sign-in-to-azure-portal"></a>登录到 Azure 门户

[登录](https://portal.azure.com) Azure 门户。

## <a name="create-cross-region-load-balancer"></a>创建跨区域负载均衡器

在本部分中，你将创建 

* 跨区域负载均衡器
* 具有全局公共 IP 地址的前端
* 具有两个区域负载均衡器的后端池

> [!IMPORTANT]
> 若要完成这些步骤，请确保已在订阅中部署了两个具有后端池的区域负载均衡器。  有关详细信息，请参阅[快速入门：使用 Azure 门户创建公共负载均衡器，以便对 VM 进行负载均衡](quickstart-load-balancer-standard-public-portal.md)。

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 在“负载均衡器”页上，选择“创建” 。

3. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | **项目详细信息** |    |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“新建”并在文本框中输入“CreateCRLBTutorial-rg” 。 |
    | **实例详细信息** |   |
    | 名称                   | 输入“myLoadBalancer-CR”                                   |
    | 区域         | 选择“(US) 美国西部”。                                        |
    | 类型          | 选择“公共”。                                        |
    | SKU           | 保留默认值“标准”。 |
    | 层           | 选择“全局” |

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="创建跨区域负载均衡器" border="true":::
  
4. 在页面底部选择“下一步: 前端 IP 配置”。

5. 在“前端 IP 配置”中，选择“+ 添加前端 IP” 。

6. 在“添加前端 IP 地址”的“名称”中输入 LoadBalancerFrontend  。

7. 对于“IP 版本”，请选择“IPv4”或“IPv6”  。

8. 在“公共 IP 地址”中，选择“新建” 。 在“名称”中输入 myPublicIP-cr 。  选择“确定” 。

9. 选择“添加”  。

10. 在页面底部选择“下一步: 后端池”。

11. 在“后端池”中，选择“+ 添加后端池” 。

12. 在“添加后端池”的“名称”中输入 myBackendPool-cr  。

13. 在“负载均衡器”的“负载均衡器”下拉框中选择“myLoadBalancer-r1”或第一个区域负载均衡器  。 验证“前端 IP 配置”和“IP 地址”是否与 myLoadBalancer-r1 相对应  。

14. 在“负载均衡器”下拉框中选择“myLoadBalancer-r2”或第二个区域负载均衡器 。 验证“前端 IP 配置”和“IP 地址”是否与 myLoadBalancer-r2 相对应  。

15. 选择 **添加** 。

16. 选择页面底部的“下一步: 入站规则”。

17. 在“入站规则”中，选择“+ 添加负载均衡规则” 。

18. 在“添加负载均衡规则”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 myHTTPRule-cr。 |
    | IP 版本 | 对于“IP 版本”，请选择“IPv4”或“IPv6”  。 |
    | 前端 IP 地址 | 选择“LoadBalancerFrontend”。 |
    | 协议 | 选择“TCP”。 |
    | 端口 | 输入 **80**。 |
    | 后端池 | 选择“myBackendPool-cr”。 |
    | 会话暂留 | 选择“无”。 |
    | 空闲超时（分钟） | 输入或将滑块移动到“15”。 |
    | TCP 重置 | 选择“启用”。  |
    | 浮动 IP | 保留默认值“禁用”。 |

19. 选择 **添加** 。

20. 在页面底部选择“查看 + 创建”。

21. 在“查看 + 创建”选项卡中，选择“创建” 。

    > [!NOTE]
    > 跨区域负载均衡器只能部署在以下主区域中：美国东部 2、美国西部、西欧、东南亚、美国中部、北欧、东亚。 有关详细信息，请参阅 **https://aka.ms/homeregionforglb**。

## <a name="test-the-load-balancer"></a>测试负载均衡器

在本部分中，你将测试跨区域负载均衡器。 你需要在 Web 浏览器中连接到公共 IP 地址。  你将停止其中一个区域负载均衡器后端池中的虚拟机并观察故障转移。

1. 在“概述”屏幕上找到负载均衡器的公共 IP 地址。 在左侧菜单中选择“所有服务”，选择“所有资源”，然后选择“myPublicIP-CR”  。

2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 IIS Web 服务器的默认页会显示在浏览器上。

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="测试负载均衡器" border="true":::

3. 停止其中一个区域负载均衡器的后端池中的虚拟机。

4. 刷新 Web 浏览器，观察与其他区域负载均衡器的连接的故障转移。

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="在故障转移之后测试负载均衡器" border="true":::

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、负载均衡器以及所有相关资源，请将其删除。 

为此，请选择包含资源的资源组“CreateCRLBTutorial-rg”，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

* 创建了跨区域负载均衡器。
* 向跨区域负载均衡器的后端池添加了区域负载均衡器。
* 创建了负载均衡规则。
* 测试了负载均衡器。

有关跨区域负载均衡器的详细信息，请参阅：
> [!div class="nextstepaction"]
> [跨区域负载均衡器（预览版）](cross-region-overview.md)
