---
title: 教程：配置端口转发 - Azure 门户
titleSuffix: Azure Load Balancer
description: 本教程介绍了如何使用 Azure 负载均衡器配置端口转发来创建与 Azure 虚拟网络中的 VM 的连接。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 8/26/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0d36be9f0ba0fc9e1b29e3fdaf0b3f9857db2204
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112081"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-azure-portal"></a>教程：使用 Azure 门户在 Azure 负载均衡器中配置端口转发

端口转发使你可以使用 Azure 负载均衡器公共 IP 地址和端口号连接到 Azure 虚拟网络中的虚拟机 (VM)。 

在本教程中，你将了解如何：

> [!div class="checklist"]
> * 创建虚拟网络和虚拟机。
> * 为后端池的出站 Internet 访问创建 NAT 网关。
> * 创建具有前端 IP、运行状况探测、后端配置、负载均衡规则和入站 NAT 规则的标准 SKU 公共负载均衡器。
> * 在 VM 上安装和配置 Web 服务器，以演示端口转发和负载均衡规则。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-virtual-network-and-virtual-machines"></a>创建虚拟网络和虚拟机

本教程中的资源需要用到虚拟网络和子网。 在本部分中，你将为后续步骤创建虚拟网络和虚拟机。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“虚拟机”。 在搜索结果中，选择“虚拟机”。

3. 在“虚拟机”中，选择“+ 创建” > “虚拟机”。
   
4. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。 </br> 输入“TutorialLBPF-rg”。 </br> 选择“确定”。 |
    | **实例详细信息** |    |
    | 虚拟机名称 | 输入“myVM1”。 |
    | 区域 | 输入“(美国)美国西部 2”。 |
    | 可用性选项 | 选择“可用性区域”。 |
    | 可用性区域 | 输入 **1**。 |
    | 映像 | 选择“Ubuntu Server 20.04 LTS - Gen2”。 |
    | Azure Spot 实例 | 保留默认值“未选中”。 |
    | 大小 | 选择 VM 大小。 |
    | **管理员帐户** |    |
    | 身份验证类型 | 选择“SSH 公钥”。 |
    | 用户名 | 输入“azureuser”。 |
    | SSH 公钥源 | 选择“生成新密钥对”。 |
    | 密钥对名称 | 输入“myKey”。 |
    | **入站端口规则** |    |
    | 公共入站端口 | 选择“无”。 |

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/create-vm-portal.png" alt-text="“创建虚拟机”的屏幕截图。":::

5. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。

6. 在“网络”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | **网络接口** |   |
    | 虚拟网络 | 选择“新建”。 </br> 在“名称”中输入“myVNet” 。 </br> 在“地址空间”的“地址范围”下，输入 10.1.0.0/16 。 </br> 在“子网”的“子网名称”下，输入“myBackendSubnet”  。 </br> 在“地址范围”中，输入 10.1.0.0/24 。 </br> 选择“确定”。 |
    | 子网 | 选择“myBackendSubnet”。 |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | 选择“高级”。 |
    | 配置网络安全组 | 选择“新建”。 </br> 在“名称”中，输入“myNSG” 。 </br> 在“入站规则”下，选择“+ 添加入站规则” 。 </br> 在“服务”中，选择“HTTP” 。 </br> 在“优先级”中，输入 100。 </br> 在“名称”中，输入“myNSGRule” 。 </br> 选择 **添加** 。 </br> 选择“确定”。 |

7. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

8. 选择“创建”。

9. 在“生成新密钥对”提示下，选择“下载私钥并创建资源” 。 下载的密钥文件名为 myKey.pem。 确保你知道 .pem 文件的下载位置，在后面的步骤中你将用到密钥文件的路径。

8. 按照步骤 1 到 8 操作，使用以下值创建另一个 VM，所有其他设置均与 myVM1 相同：

    | 设置 | VM 2 |
    | ------- | ----- |
    | **基础知识** |    |
    | **实例详细信息** |   |
    | 虚拟机名称 | **myVM2** |
    | 可用性区域 | **2** |
    | **管理员帐户** |   |
    | 身份验证类型 | **SSH 公钥** |
    | SSH 公钥源 | 选择“使用 Azure 中存储的现有密钥”。 |
    | 已存储密钥 | 选择“myKey”。 |
    | **联网** |   |
    | **网络接口** |  |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | 选择“高级”。 |
    | 配置网络安全组 | 选择现有的“myNSG” |

## <a name="create-nat-gateway"></a>创建 NAT 网关

在本部分中，你将为虚拟网络中的资源创建用于出站 Internet 访问的 NAT 网关。 

1. 在门户顶部的搜索框中，输入“NAT 网关”。 在搜索结果中选择“NAT 网关”。

2. 在“NAT 网关”中，选择“+ 创建” 。

3. 在“创建网络地址转换(NAT)网关”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“TutorialLBPF-rg”。 |
    | **实例详细信息** |    |
    | NAT 网关名称 | 输入 myNATGateway。 |
    | 可用性区域 | 选择“无”。 |
    | 空闲超时（分钟） | 输入 **15**。 |

4. 选择“出站 IP”选项卡，或者选择页面底部的“下一步: 出站 IP”按钮 。

5. 在“出站 IP”选项卡中，选择“公共 IP 地址”旁边的“创建新的公共 IP 地址”  。

6. 在“添加公共 IP 地址”的“名称”中，输入“myNATGatewayIP”  。

7. 选择“确定”。

8. 选择“子网”选项卡，或者选择页面底部的“下一步: 子网”按钮 。

9. 在“子网”选项卡的“虚拟网络”中，选择“myVNet”  。

10. 在“子网名称”下选择“myBackendSubnet” 。

11. 选择页面底部的“查看 + 创建”蓝色按钮，或选择“查看 + 创建”选项卡 。

12. 选择“创建”。

## <a name="create-load-balancer"></a>创建负载均衡器

在本部分中，你将创建负载均衡器。 创建过程中将配置前端 IP、后端池、负载均衡和入站 NAT 规则。

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 在“负载均衡器”页上，选择“创建” 。

3. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | **项目详细信息** |   |
    | 订阅               | 选择订阅。    |    
    | 资源组         | 选择“TutorialLBPF-rg”。 |
    | **实例详细信息** |   |
    | 名称                   | 输入“myLoadBalancer”                                   |
    | 区域         | 选择“(US) 美国西部 2”。                                        |
    | 类型          | 选择“公共”。                                        |
    | SKU           | 保留默认值“标准”。 |
    | 层          | 保留默认值“区域”。 |


4. 选择页面底部的“下一步: 前端 IP 配置”。

5. 在“前端 IP 配置”中，选择“+ 添加前端 IP” 。

6. 在“名称”中输入“LoadBalancerFrontend” 。

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
    > 在提供[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)设置的区域中，可以选择无区域（默认选项）、特定区域或区域冗余。 请根据特定的域故障要求做出选择。 在不提供“可用性区域”设置的区域中，不会显示此字段。 </br> 有关可用性区域的详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

12. 保留“路由首选项”的默认值“Microsoft 网络” 。

13. 选择“确定” 。

14. 选择“添加”  。

15. 选择页面底部的“下一步: 后端池”。

16. 在“后端池”选项卡上，选择“+ 添加后端池” 。

17. 在“添加后端池”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入“myBackendPool”。 |
    | 虚拟网络 | 选择“myVNet (TutorialLBPF-rg)”。 |
    | 后端池配置 | 选择“NIC”。 |
    | IP 版本 | 选择“IPv4”。 |

18. 在“虚拟机”中，选择“+ 添加”。

19. 在“向后端池添加虚拟机”中，选中 myVM1 和 myVM2 旁边的复选框  。

20. 选择 **添加** 。

21. 选择 **添加** 。

22. 选择页面底部的“下一步: 入站规则”按钮。

23. 在“入站规则”选项卡的“负载均衡规则”中，选择“+ 添加负载均衡规则”  。

24. 在“添加负载均衡规则”中，输入或选择以下信息。

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
    | 空闲超时（分钟） | 输入或选择 15。 |
    | TCP 重置 | 选择“启用”。  |
    | 浮动 IP | 选择“已禁用”。  |
    | 出站源网络地址转换 (SNAT) | 保留默认值“(建议)使用出站规则为后端池成员提供对 Internet 的访问权限。” |

25. 选择 **添加** 。

26. 在“入站规则”选项卡的“入站 NAT 规则”中，选择“+ 添加入站 NAT 规则”。

27. 在“添加入站 NAT 规则”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入“myNATRuleVM1-221”。 |
    | 前端 IP 地址 | 选择“LoadBalancerFrontend”。 |
    | 服务 | 选择“自定义”。 |
    | 协议 | 保留默认值“TCP”。 |
    | 空闲超时(分钟) | 输入或选择 15。 |
    | TCP 重置 | 选择“启用”。 |
    | 端口 | 输入 221。 |
    | 目标虚拟机 | 选择 myVM1。 |
    | 网络 IP 配置 | 选择“ipconfig1 (10.1.0.4)”。 |
    | 端口映射 | 选择“自定义”。 |
    | 浮动 IP | 保留默认值“禁用”。 |
    | 目标端口 | 输入 22。 |

28. 选择 **添加** 。

29. 选择“+ 添加入站 NAT 规则”。

30. 在“添加入站 NAT 规则”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入“myNATRuleVM2-222”。 |
    | 前端 IP 地址 | 选择“LoadBalancerFrontend”。 |
    | 服务 | 选择“自定义”。 |
    | 协议 | 保留默认值“TCP”。 |
    | 空闲超时(分钟) | 输入或选择 15。 |
    | TCP 重置 | 选择“启用”。 |
    | 端口 | 输入 222。 |
    | 目标虚拟机 | 选择“myVM2”。 |
    | 网络 IP 配置 | 选择“ipconfig1 (10.1.0.5)”。 |
    | 端口映射 | 选择“自定义”。 |
    | 浮动 IP | 保留默认值“禁用”。 |
    | 目标端口 | 输入 22。 |

31. 选择 **添加** 。

32. 选择页面底部的“查看 + 创建”蓝色按钮。

33. 选择“创建”。

## <a name="install-web-server"></a>安装 Web 服务器

在本部分中，你将根据入站 NAT 规则通过 SSH 连接到虚拟机，并安装 Web 服务器。

1. 在门户顶部的搜索框中，输入“负载均衡器”。 在搜索结果中选择“负载均衡器”。

2. 选择“myLoadBalancer”。

3. 在 myLoadBalancer 的“概述”页中，记下“公共 IP 地址”。  在本示例中，它为 20.190.2.163。

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/get-public-ip.png" alt-text="Azure 门户中公共 IP 的屏幕截图。":::

4. 如果使用的是 Mac 或 Linux 计算机，请打开 Bash 提示符。 如果使用的是 Windows 计算机，请打开 PowerShell 提示符。

5. 在提示符下，通过 SSH 连接到 myVM1。 将 IP 地址替换为前面步骤中检索到的地址以及用于 myVM1 入站 NAT 规则的端口 221。 将 .pem 的路径替换为密钥文件下载位置的路径。

    ```console
    ssh -i .\Downloads\myKey.pem azureuser@20.190.2.163 -p 221
    ```

    > [!TIP]
    > 下次在 Azure 中创建 VM 时，可以使用此次创建的 SSH 密钥。 下次创建 VM 时，只需为“SSH 公钥源”选择“使用存储在 Azure 中的密钥” 。 你的计算机上已有私钥，因此无需下载任何内容。

6. 在 SSH 会话中更新包源，然后安装最新的 NGINX 包。

    ```bash
    sudo apt-get -y update
    sudo apt-get -y install nginx
    ``` 

7. 输入 `Exit` 退出 SSH 会话

8. 在提示符下，通过 SSH 连接到 myVM2。 将 IP 地址替换为前面步骤中检索到的地址以及用于 myVM2 入站 NAT 规则的端口 222。 将 .pem 的路径替换为密钥文件下载位置的路径。

    ```console
    ssh -i .\Downloads\myKey.pem azureuser@20.190.2.163 -p 222
    ```

9. 在 SSH 会话中更新包源，然后安装最新的 NGINX 包。

    ```bash
    sudo apt-get -y update
    sudo apt-get -y install nginx
    ``` 

10. 输入 `Exit` 退出 SSH 会话。

## <a name="test-the-web-server"></a>测试 Web 服务器

你将在本部分中打开 Web 浏览器，输入前面步骤中检索到的负载均衡器的 IP 地址。

1. 打开 Web 浏览器。

2. 在地址栏中，输入负载均衡器的 IP 地址。 在本示例中，它为 20.190.2.163。

3. 系统将显示默认的 NGINX 网站。

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/web-server-test.png" alt-text="测试 NGINX Web 服务器的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除虚拟机和负载均衡器：

1. 在门户顶部的搜索框中输入“资源组”。  在搜索结果中选择“资源组”。

2. 在“资源组”中选择“TutorialLBPF-rg”。

3. 选择“删除资源组”  。

4. 在“键入资源组名称:”中输入“TutorialLBPF-rg” 。 选择“删除”。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何创建跨区域负载均衡器：

> [!div class="nextstepaction"]
> [使用 Azure 门户创建跨区域负载均衡器](tutorial-cross-region-portal.md)
