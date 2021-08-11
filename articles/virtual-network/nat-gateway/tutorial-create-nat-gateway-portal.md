---
title: 教程：创建 NAT 网关 - Azure 门户
titlesuffix: Azure Virtual Network NAT
description: 本教程介绍如何使用 Azure 门户创建并验证 NAT 网关。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438758"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>教程：使用 Azure 门户创建 NAT 网关

本教程介绍如何使用 Azure 虚拟网络 NAT 服务。 你将创建一个 NAT 网关，以便为 Azure 中的虚拟机提供出站连接。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建虚拟网络。
> * 创建虚拟机。
> * 创建 NAT 网关并将其与虚拟网络关联。
> * 连接到虚拟机并验证 NAT IP 地址。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="virtual-network"></a>虚拟网络

在部署 VM 并使用 NAT 网关之前，需要创建资源组和虚拟网络。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在屏幕的左上方选择“创建资源”>“网络”>“虚拟网络”，或者在搜索框中搜索“虚拟网络”。  

3. 选择“创建”。

4. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅                                  |
    | 资源组   | 选择“新建”。 </br> 输入“myResourceGroupNAT”。 </br> 选择“确定”。 |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入“myVNet”                                    |
    | 区域           | 选择“(欧洲)西欧” |

5. 选择“IP 地址”选项卡  ，或选择页面底部的“下一步:  IP 地址”按钮。

6. 在“IP 地址”  选项卡上，输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | IPv4 地址空间 | 输入“10.1.0.0/16” |

7. 选择“+ 添加子网”。 

8. 在“编辑子网”中输入以下信息： 

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | 子网名称 | 输入 **mySubnet** |
    | 子网地址范围 | 输入“10.1.0.0/24” |

9. 选择 **添加** 。

10. 选择“安全”选项卡。

11. 在“BastionHost”下，选择“启用” 。 输入此信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名称 | 输入“myBastionHost” |
    | AzureBastionSubnet 地址空间 | 输入“10.1.1.0/24” |
    | 公共 IP 地址 | 选择“新建”。 </br> 对于“名称”，请输入“myBastionIP” 。 </br> 选择“确定”。 |

12. 选择“查看 + 创建”选项卡，或选择“查看 + 创建”按钮。

13. 选择“创建”。

## <a name="nat-gateway"></a>NAT 网关

可以使用一个或多个公共 IP 地址资源和/或公共 IP 前缀。 我们将添加公共 IP 资源和 NAT 网关资源。

1. 在屏幕的左上方，选择“创建资源”>“网络”>“NAT 网关”，或者在搜索框中搜索“NAT 网关” 。

2. 选择“创建”。 

3. 在“创建网络地址转换(NAT)网关”中，在“基本信息”选项卡中输入或选择以下信息 ：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅。                                  |
    | 资源组   | 选择“myResourceGroupNAT”。 |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入“myNATgateway”                                    |
    | 区域           | 选择“(欧洲)欧洲西部”  |
    | 可用性区域 | 选择“无”。 |
    | 空闲超时（分钟） | 输入“10”。 |

4. 选择“出站 IP”选项卡，或者选择“下一步:出站 IP”按钮（位于页面底部） 。

5. 在“出站 IP”选项卡中，输入或选择以下信息：

    | **设置** | **值** |
    | ----------- | --------- |
    | 公共 IP 地址 | 选择“创建新的公共 IP 地址”。 </br> 在“名称”中，选择“myPublicIP” 。 </br> 选择“确定”。 |

6. 选择“子网”选项卡，或者选择“下一步:子网”按钮（位于页面底部） 。

7. 在“子网”选项卡中，选择“虚拟网络”下拉列表中的“myVNet”  。

8. 选中“mySubnet”旁边的复选框。

9. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

10. 选择“创建”。
    
## <a name="virtual-machine"></a>虚拟机

在本部分中，你将创建虚拟机来测试 NAT 网关并验证出站连接的公共 IP 地址。

1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”  。 

2. 在“基本信息”选项卡的“创建虚拟机”页中，输入或选择以下信息 ：

    | **设置** | **值** |
    | ----------- | --------- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroupNAT”。 |
    | **实例详细信息** |   |
    | 虚拟机名称 | 输入“myVM”。 |
    | 区域 | 选择“(欧洲)欧洲西部”。 |
    | 可用性选项 | 保留默认值“无需冗余”。 |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen1”。 |
    | 大小 | 选择“Standard_DS1_v2”。 |
    | **管理员帐户** |   |
    | 用户名 | 输入虚拟机的用户名。 |
    | Password | 输入密码。 |
    | 确认密码 | 确认密码。 |
    | **入站端口规则** |    |
    | 公共入站端口 | 选择“无”。 |

3. 选择“磁盘”选项卡，或者选择“下一步:磁盘”按钮（位于页面底部） 。

4. 在“磁盘”选项卡中保留默认值。

5. 选择“网络”选项卡，或者选择“下一步:网络”按钮（位于页面底部） 。

6. 在“网络”中，输入或选择以下信息：

    | **设置** | **值** |
    | ----------- | --------- |
    | **网络接口** |   |
    | 虚拟网络 | 选择“myVNet”。 |
    | 子网 | 选择“mySubnet”。 |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | 选择“基本”。 |
    | 公共入站端口 | 选择“无”。 |

7. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

8. 选择“创建”。

## <a name="test-nat-gateway"></a>测试 NAT 网关

在本部分中，我们将测试 NAT 网关。 首先，我们将发现 NAT 网关的公共 IP。 然后，我们将连接到测试虚拟机，并通过 NAT 网关验证出站连接。
    
1. 在“概述”屏幕上找到 NAT 网关的公共 IP 地址。 在左侧菜单中选择“所有服务”，选择“所有资源”，然后选择“myPublicIP”。

2. 记下公共 IP 地址：

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="发现 NAT 网关的公共 IP 地址" border="true":::

3. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后从资源列表中选择位于“myResourceGroupNAT”资源组中的“myVM”   。

4. 在“概述”页上，选择“连接”，然后选择“Bastion”  。

5. 选择蓝色的“使用堡垒”按钮。

6. 输入在 VM 创建过程中输入的用户名和密码。

7. 在 myTestVM 中打开 Internet Explorer 。

8. 在地址栏中输入“https://whatsmyip.com”。

9. 验证显示的 IP 地址与你在上一步中记下的 NAT 网关地址是否匹配：

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="显示外部出站 IP 的 Internet Explorer" border="true":::

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除虚拟网络、虚拟机和 NAT 网关：

1. 从左侧菜单中，选择“资源组”。

2. 选择“myResourceGroupNAT”资源组。

3. 选择“删除资源组”。

4. 输入“myResourceGroupNAT”，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

有关 Azure 虚拟网络 NAT 的详细信息，请参阅：
> [!div class="nextstepaction"]
> [虚拟网络 NAT 概述](nat-overview.md)
