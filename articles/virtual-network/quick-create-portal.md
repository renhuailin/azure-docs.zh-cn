---
title: 快速入门：创建虚拟网络 - Azure 门户
titleSuffix: Azure Virtual Network
description: 本快速入门介绍如何使用 Azure 门户创建虚拟网络。
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: kumud
ms.openlocfilehash: 8af5b302e3ec790b6ee9356aca0699d0edcd284e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606051"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建虚拟网络

本快速入门介绍如何使用 Azure 门户创建虚拟网络。 我们部署两个虚拟机 (VM)。 接下来就可以在 VM 之间安全地通信，并通过 Internet 连接到 VM。 虚拟网络是 Azure 中专用网络的基本构建块。 它能让 Azure 资源（例如 VM）互相安全通信以及与 Internet 通信。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 在门户的左上角选择“创建资源”。

2. 在搜索框中，输入“虚拟网络”。 在搜索结果中，选择“虚拟网络”。

3. 在“虚拟网络”页中选择“创建” 。

4. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。  </br> 输入“myResourceGroup”。 </br> 选择“确定”。 |
    | **实例详细信息** |   |
    | 名称 | 输入 **myVNet**。 |
    | 区域 | 选择“(US)美国东部”。 |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="创建虚拟网络 Azure 门户" border="true":::

5. 选择“IP 地址”选项卡，或选择页面底部的“下一步: IP 地址”按钮。

6. 在“IPv4 地址空间”中，选择现有地址空间，并将其更改为 **10.1.0.0/16**。

7. 选择“+ 添加子网”，然后输入“Subnet”作为“子网名称”，输入“10.1.0.0/24”作为“子网地址范围”。

8. 选择 **添加** 。

9. 选择“安全性”选项卡，或者选择“下一步: 安全性”按钮（位于页面底部）。

10. 在“BastionHost”下，选择“启用” 。 输入此信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名称 | 输入“myBastionHost” |
    | AzureBastionSubnet 地址空间 | 输入“10.1.1.0/24” |
    | 公共 IP 地址 | 选择“新建”。 </br> 对于“名称”，请输入“myBastionIP” 。 </br> 选择“确定”。 |

11. 选择“查看 + 创建”选项卡，或选择“查看 + 创建”按钮。

12. 选择“创建”。

## <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个 VM：

### <a name="create-the-first-vm"></a>创建第一个 VM

1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”  。 
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroup” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM1” |
    | 区域 | 选择“(US)美国东部” |
    | 可用性选项 | 选择“无需基础结构冗余” |
    | 映像 | 选择“Windows Server 2019 Datacenter” |
    | Azure Spot 实例 | 请选择“否” |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |
    | **入站端口规则** |    |
    | 公共入站端口 | 选择“无”。 |
    |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | 选择“myVNet”。 |
    | 子网 | 选择“mySubnet” |
    | 公共 IP | 选择“无” |
    | NIC 网络安全组 | 选择“基本”|
    | 公共入站端口网络 | 选择“无”。 |
   
5. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。
  
6. 检查设置，然后选择“创建”。

### <a name="create-the-second-vm"></a>创建第二个 VM

1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”  。 
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroup” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM2” |
    | 区域 | 选择“(US)美国东部” |
    | 可用性选项 | 选择“无需基础结构冗余” |
    | 映像 | 选择“Windows Server 2019 Datacenter” |
    | Azure Spot 实例 | 请选择“否” |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |
    | **入站端口规则** |    |
    | 公共入站端口 | 选择“无”。 |
    |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | 选择“myVNet”。 |
    | 子网 | 选择“mySubnet” |
    | 公共 IP | 选择“无” |
    | NIC 网络安全组 | 选择“基本”|
    | 公共入站端口网络 | 选择“无”。 |
   
5. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。
  
6. 检查设置，然后选择“创建”。

## <a name="connect-to-myvm1"></a>连接到 myVM1

1. 转到 [Azure 门户](https://portal.azure.com)来管理专用 VM。 搜索并选择“虚拟机”。

2. 选取专用虚拟机“myVM1”的名称。

3. 在 VM 菜单栏中，选择“连接”，然后选择“堡垒”。

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="用 Azure Bastion 连接到 myVM1" border="true":::

4. 在“连接”页中，选择蓝色的“使用堡垒”按钮。

5. 在“堡垒”页上，输入以前为虚拟机创建的用户名和密码。

6. 选择“连接”  。

## <a name="communicate-between-vms"></a>VM 之间进行通信

1. 在“myVM1”的堡垒连接中，打开 PowerShell。

2. 输入 `ping myvm2`。

    会收到类似于以下输出的消息：

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. 关闭到“myVM1”的堡垒连接。

4. 完成“[连接到 myVM1](#connect-to-myvm1)”中的步骤，但连接到“myVM2”。

5. 在“myVM2”上打开 PowerShell，输入 `ping myvm1`。

    你将收到类似于以下消息的内容：

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. 关闭到“myVM2”的堡垒连接。

## <a name="clean-up-resources"></a>清理资源

在本快速入门中，你创建了默认的虚拟网络和两个 VM。 

从 Internet 连接到了其中一个 VM，并在两个 VM 之间安全地进行了通信。

使用虚拟网络和 VM 之后，请删除资源组和其包含的所有资源：

1. 搜索并选择“myResourceGroup”。

1. 选择“删除资源组”。

1. 对于“键入资源组名称”，请输入“myResourceGroup”，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟网络设置，请参阅[创建、更改或删除虚拟网络](manage-virtual-network.md)。

若要详细了解 VM 网络通信类型，请参阅[筛选网络流量](tutorial-filter-network-traffic.md)。
