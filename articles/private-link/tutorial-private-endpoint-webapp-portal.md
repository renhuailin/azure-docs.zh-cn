---
title: 教程：使用 Azure 专用终结点连接到 Web 应用
titleSuffix: Azure Private Link
description: 在本教程中开始使用 Azure 专用终结点以私密方式连接到 Web 应用。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 67c4b96ebb9e578e6a617b37e5a05084e72c4f56
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "114285059"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>教程：使用 Azure 专用终结点连接到 Web 应用

Azure 专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络和 Bastion 主机。
> * 创建虚拟机。
> * 创建 Web 应用。
> * 创建专用终结点。
> * 测试到 Web 应用专用终结点的连接。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!Note]
> 专用终结点可在公共区域中用于高级 V2 层级、高级 V3 层级 Windows Web 应用、Linux Web 应用和 Azure Functions 高级计划（有时被称为弹性高级计划）。 

## <a name="prerequisites"></a>必备条件

* Azure 订阅

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-virtual-network-and-bastion-host"></a>创建虚拟网络和堡垒主机

在本部分中，你将创建虚拟网络、子网和堡垒主机。 

堡垒主机将用于安全地连接到虚拟机，以测试专用终结点。

1. 在屏幕的左上方选择“创建资源”>“网络”>“虚拟网络”，或者在搜索框中搜索“虚拟网络”。  

2. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅                                  |
    | 资源组   | 选择“myResourceGroup” |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入“myVNet”                                    |
    | 区域           | 选择“西欧” |

3. 选择“IP 地址”选项卡  ，或选择页面底部的“下一步:  IP 地址”按钮。

4. 在“IP 地址”  选项卡上，输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | IPv4 地址空间 | 输入“10.1.0.0/16” |

5. 在“子网名称”下，选择词语“默认”。

6. 在“编辑子网”中输入以下信息： 

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | 子网名称 | 输入 **mySubnet** |
    | 子网地址范围 | 输入“10.1.0.0/24” |

7. 选择“保存” 。

8. 选择“安全”选项卡。

9. 在“BastionHost”下，选择“启用” 。 输入此信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名称 | 输入“myBastionHost” |
    | AzureBastionSubnet 地址空间 | 输入“10.1.1.0/24” |
    | 公共 IP 地址 | 选择“新建”。 </br> 对于“名称”，请输入“myBastionIP” 。 </br> 选择“确定”。 |


8. 选择“查看 + 创建”选项卡，或选择“查看 + 创建”按钮。

9. 选择“创建”。

## <a name="create-a-virtual-machine"></a>创建虚拟机

在本部分中，你将创建将用来测试专用终结点的虚拟机。


1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”，或者在搜索框中搜索“虚拟机”。   
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroup” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM” |
    | 区域 | 选择“西欧” |
    | 可用性选项 | 选择“无需基础结构冗余” |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen1” |
    | Azure Spot 实例 | 请选择“否” |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | myVNet |
    | 子网 | **mySubnet** |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | **基本**|
    | 公共入站端口 | 选择“无”。 |
   
5. 选择“查看 + 创建”。 
  
6. 检查设置，然后选择“创建”。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-web-app"></a>创建 Web 应用

在本部分中，你将创建一个 Web 应用。

1. 在左侧菜单中，选择“创建资源” > “存储” > “Web 应用”，或者在搜索框中搜索“Web 应用”。   

2. 在“创建 Web 应用”的“基本信息”选项卡中，输入或选择以下信息 ：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroup” |
    | **实例详细信息** |  |
    | 名称 | 输入 mywebapp。 如果该名称不可用，请输入一个唯一的名称。 |
    | 发布 | 选择“代码”。 |
    | 运行时堆栈 | 选择“.NET Core 3.1 (LTS)”。 |
    | 操作系统 | 选择“Windows”。 |
    | 区域 | 选择“西欧” |
    | **应用服务计划** |  |
    | Windows 计划（西欧） | 选择“新建”。 </br> 在“名称”中输入 myServicePlan 。 |
    | SKU 和大小 | 选择“更改大小”。 </br> 在“规格选取器”屏幕中选择“P2V2” 。 </br> 选择“应用”。 |
   
3. 选择“查看 + 创建”。

4. 选择“创建”。

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Azure 门户中“创建 Web 应用”的“基本信息”选项卡。" border="true":::

## <a name="create-private-endpoint"></a>创建专用终结点

1. 在左侧菜单中，选择“所有资源” > “mywebapp”或在创建过程中选择的名称 。

2. 在 Web 应用概述中，选择“设置” > “网络” 。

3. 在“网络”中，选择“配置专用终结点连接” 。

4. 在“专用终结点连接”屏幕中选择“+ 添加” 。

5. 在“添加专用终结点”屏幕中输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 mywebappendpoint。 |
    | 订阅 | 选择订阅。 |
    | 虚拟网络 | 选择“myVNet”。 |
    | 子网 | 选择“mySubnet”。 |
    | 与专用 DNS 区域集成 | 请选择“是”。 |

6. 选择“确定” 。
    

## <a name="test-connectivity-to-private-endpoint"></a>测试到专用终结点的连接

在本部分中，你将使用在上一步骤中创建的虚拟机通过专用终结点连接到 Web 应用。

1. 在左侧导航窗格中选择“资源组”。

2. 选择“myResourceGroup”。

3. 选择“myVM”。

4. 在 **myVM** 的“概述”页上，选择“连接”，然后选择“堡垒”。

5. 选择蓝色的“使用堡垒”按钮。

6. 输入在创建虚拟机期间输入的用户名和密码。

7. 连接后，在服务器上打开 Windows PowerShell。

8. 输入 `nslookup <webapp-name>.azurewebsites.net`。 将 \<webapp-name> 替换为在之前的步骤中创建的 Web 应用的名称。  你将收到类似于以下所示内容的消息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    将为 Web 应用名称返回专用 IP 地址 10.1.0.5。  此地址位于你之前创建的虚拟网络的子网中。

9. 在本地计算机上打开 Web 浏览器并输入 Web 应用的外部 URL： https://\<webapp-name>.azurewebsites.net。

10. 验证是否收到 403 页面。 此页面指示无法从外部访问 Web 应用。

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="外部 Web 应用地址的 403 页面。" border="true":::

11. 在到 myVM 的堡垒连接中，打开 Internet Explorer。

12. 输入 Web 应用的 url： https://\<webapp-name>.azurewebsites.net。

13. 验证是否收到默认 Web 应用页面。

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="默认 Web 应用页面。" border="true":::

18. 关闭到 **myVM** 的连接。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除虚拟网络、虚拟机和 Web 应用：

1. 从左侧菜单中，选择“资源组”。

2. 选择“myResourceGroup”。

3. 选择“删除资源组”。

4. 在“键入资源组名称”中输入“myResourceGroup”。 

5. 选择“删除” 。

## <a name="next-steps"></a>后续步骤

了解如何创建专用链接服务：
> [!div class="nextstepaction"]
> [创建专用链接服务](create-private-link-service-portal.md)
