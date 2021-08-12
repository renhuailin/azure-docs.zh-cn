---
title: 快速入门 - 使用 Azure 门户创建专用终结点
description: 借助本快速入门了解如何使用 Azure 门户创建专用终结点。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 02d9ca3ba6a16e06c4357a46a050f8803147aaaf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "114292876"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建专用终结点

使用专用终结点安全连接到 Azure Web 应用以开始使用 Azure 专用链接。

在本快速入门中，你将创建 Azure Web 应用的专用终结点，并部署虚拟机以测试专用连接。  

可以为不同类型的 Azure 服务（例如 Azure SQL 和 Azure 存储）创建专用终结点。

## <a name="prerequisites"></a>必备条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure 订阅中部署了 PremiumV2 层或更高版本应用服务计划的 Azure Web 应用。  
    * 有关详细信息及示例，请参阅[快速入门：在 Azure 中创建 ASP.NET Core Web 应用](../app-service/quickstart-dotnetcore.md)。 
    * 有关创建 Web 应用和终结点的详细教程，请参阅[教程：使用 Azure 专用终结点连接到 Web 应用](tutorial-private-endpoint-webapp-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-virtual-network-and-bastion-host"></a>创建虚拟网络和堡垒主机

在本部分中，你将创建虚拟网络、子网和堡垒主机。 

堡垒主机将用于安全地连接到虚拟机，以测试专用终结点。

1. 在屏幕的左上方选择“创建资源”>“网络”>“虚拟网络”，或者在搜索框中搜索“虚拟网络”。  

2. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅                                  |
    | 资源组   | 选择“CreatePrivateEndpointQS-rg” |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入“myVNet”                                    |
    | 区域           | 选择“西欧”。|

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
    | 资源组 | 选择“CreatePrivateEndpointQS-rg” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM” |
    | 区域 | 选择“西欧”。 |
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

## <a name="create-a-private-endpoint"></a>创建专用终结点

在本部分中，将为先决条件部分中创建的 Web 应用创建专用终结点。

1. 在门户中的屏幕左上方，选择“创建资源” > “网络” > “专用链接”，或在搜索框中输入“专用链接”   。

2. 选择“创建”。

3. 在“专用链接中心”中，选择左侧菜单中的“专用终结点” 。

4. 在“专用终结点”中，选择“+ 添加” 。

5. 在“创建专用终结点”的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“CreatePrivateEndpointQS-rg”。 我们在上一部分创建了此资源组。|
    | **实例详细信息** |  |
    | 名称  | 输入“myPrivateEndpoint”。 |
    | 区域 | 选择“西欧”。 |

6. 选择“资源”选项卡或页面底部的“下一步: 资源”按钮。
    
7. 在“资源”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 连接方法 | 选择“连接到我的目录中的 Azure 资源”。 |
    | 订阅 | 选择订阅。 |
    | 资源类型 | 选择“Microsoft.Web/sites”。 |
    | 资源 | 选择 \<your-web-app-name>。 </br> 选择在先决条件中创建的 Web 应用的名称。 |
    | 目标子资源 | 选择“站点”。 |

8. 选择“配置”选项卡或屏幕底部的“下一步: 配置”按钮。

9. 在“配置”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **联网** |  |
    | 虚拟网络 | 选择“myVNet”。 |
    | 子网 | 选择“mySubnet”。 |
    | **专用 DNS 集成** |  |
    | 与专用 DNS 区域集成 | 保留默认值“是”。 |
    | 订阅 | 选择订阅。 |
    | 专用 DNS 区域 | 保留默认值“(新建) privatelink.azurewebsites.net”。
    

13. 选择“查看 + 创建”。

14. 选择“创建”。

## <a name="test-connectivity-to-private-endpoint"></a>测试到专用终结点的连接

在本部分中，你将使用在上一步骤中创建的虚拟机通过专用终结点连接到 Web 应用。

1. 在左侧导航窗格中选择“资源组”。

2. 选择“CreatePrivateEndpointQS-rg”。

3. 选择“myVM”。

4. 在 **myVM** 的“概述”页上，选择“连接”，然后选择“堡垒”。

5. 选择蓝色的“使用堡垒”按钮。

6. 输入在创建虚拟机期间输入的用户名和密码。

7. 连接后，在服务器上打开 Windows PowerShell。

8. 输入 `nslookup <your-webapp-name>.azurewebsites.net`。 将 \<your-webapp-name> 替换为在之前的步骤中创建的 Web 应用的名称。  你将收到类似于以下所示内容的消息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    将为 Web 应用名称返回专用 IP 地址 10.1.0.5。  此地址位于你之前创建的虚拟网络的子网中。

11. 在到 myVM 的堡垒连接中，打开 Internet Explorer。

12. 输入 Web 应用的 URL： https://\<your-webapp-name>.azurewebsites.net。

13. 如果你的应用程序尚未部署，你将收到默认 Web 应用页：

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="默认 Web 应用页面。" border="true":::

18. 关闭到 **myVM** 的连接。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除虚拟网络、虚拟机和 Web 应用：

1. 从左侧菜单中，选择“资源组”。

2. 选择“CreatePrivateEndpointQS-rg”。

3. 选择“删除资源组”。

4. 在“键入资源组名称”中输入 CreatePrivateEndpointQS 。

5. 选择“删除” 。


## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了：

* 虚拟网络和堡垒主机。
* 虚拟机。
* Azure Web 应用的专用终结点。

你使用虚拟机通过专用终结点安全测试了到 Web 应用的连接。



有关支持专用终结点的服务的详细信息，请参阅：
> [!div class="nextstepaction"]
> [专用链接可用性](private-link-overview.md#availability)
