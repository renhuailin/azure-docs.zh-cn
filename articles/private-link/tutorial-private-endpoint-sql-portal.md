---
title: 教程：使用 Azure 专用终结点连接到 Azure SQL 服务器 - 门户
description: 借助本教程了解如何使用 Azure 门户创建具有专用终结点的 Azure SQL 服务器。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 6a18c9e0dc8f7d6f78985b626fa36c84fe15876c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "114292870"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>教程：使用 Azure 专用终结点连接到 Azure SQL 服务器 - Azure 门户

Azure 专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络和 Bastion 主机。
> * 创建虚拟机。
> * 创建 Azure SQL 服务器和专用终结点。
> * 测试到 SQL 服务器专用终结点的连接。

## <a name="prerequisites"></a>必备条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

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
    | 资源组   | 选择“CreateSQLEndpointTutorial-rg” |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入“myVNet”                                    |
    | 区域           | 选择“美国东部” |

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
    | 资源组 | 选择“CreateSQLEndpointTutorial” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM” |
    | 区域 | 选择“美国东部” |
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

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>创建 Azure SQL 服务器和专用终结点

本部分将在 Azure 中创建一个 SQL 服务器。 

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “数据库” > “SQL 数据库”。  

1. 在“创建 SQL 数据库”的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“CreateSQLEndpointTutorial”。 我们在上一部分创建了此资源组。|
    | **数据库详细信息** |  |
    | 数据库名称  | 输入 mysqldatabase。 如果此名称已被使用，请创建唯一的名称。 |
    | 服务器 | 选择“新建”。 |

6. 在“新建服务器”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 服务器名称  | 输入 **mysqlserver**。 如果此名称已被使用，请创建唯一的名称。|
    | 服务器管理员登录名 | 输入所选的管理员名称。 |
    | 密码 | 输入所选密码。 密码长度必须至少为 8 个字符，且符合定义的要求。 |
    | 位置 | 选择“美国东部” |
    
7. 选择“确定” 。

8. 选择“网络”选项卡，或选择“下一步: 网络”按钮。

9. 在“网络”选项卡中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **网络连接** | |
    | 连接方法 | 选择“专用终结点”。 |
   
10. 在“专用终结点”中选择“+ 添加专用终结点” 。

11. 在“创建专用终结点”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“CreateSQLEndpointTutorial”。 |
    | 位置 | 选择“美国东部”。 |
    | 名称 | 输入 myPrivateSQLendpoint。 |
    | 目标子资源 | 选择“SQLServer”。 |
    | **网络** |  |
    | 虚拟网络 | 选择“myVNet”。 |
    | 子网 | 选择“mySubnet”。 |
    | **专用 DNS 集成** | |
    | 与专用 DNS 区域集成 | 保留默认值“是”。 |
    | 专用 DNS 区域 | 保留默认值“(新建) privatelink.database.windows.net”。 |

12. 选择“确定” 。 

13. 选择“查看 + 创建”。

14. 选择“创建”。

## <a name="test-connectivity-to-private-endpoint"></a>测试到专用终结点的连接

本部分将使用在上一步骤中创建的虚拟机通过专用终结点连接到 SQL 服务器。

1. 在左侧导航窗格中选择“资源组”。

2. 选择“CreateSQLEndpointTutorial”。

3. 选择“myVM”。

4. 在 **myVM** 的“概述”页上，选择“连接”，然后选择“堡垒”。

5. 选择蓝色的“使用堡垒”按钮。

6. 输入在创建虚拟机期间输入的用户名和密码。

7. 连接后，在服务器上打开 Windows PowerShell。

8. 输入 `nslookup <sqlserver-name>.database.windows.net`。 将 \<sqlserver-name> 替换为在上一步骤中创建的 SQL 服务器的名称。  你将收到类似于以下所示内容的消息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    将为 SQL 服务器名称返回专用 IP 地址 10.1.0.5。  此地址位于你之前创建的虚拟网络的子网中。


9. 在 myVM 上安装 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017)。

10. 打开 SQL Server Management Studio  。

4. 在“连接服务器”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 服务器类型 | 选择“数据库引擎”。|
    | 服务器名称 | 输入 \<sqlserver-name>.database.windows.net |
    | 身份验证 | 选择“SQL Server 身份验证”。 |
    | 用户名 | 输入在服务器创建过程中所输入的用户名 |
    | Password | 输入在服务器创建过程中所输入的密码 |
    | 记住密码 | 请选择“是”。 |

1. 选择“连接”  。
2. 浏览左侧菜单中的数据库。
3. （可选）创建或查询 mydatabase 中的信息。
4. 关闭与 **myVM** 的远程桌面连接。 

## <a name="clean-up-resources"></a>清理资源 
用完专用终结点、SQL 服务器和 VM 之后，请删除资源组及其包含的所有资源： 
1. 在门户顶部的“搜索”框中输入 CreateSQLEndpointTutorial，然后从搜索结果中选择“CreateSQLEndpointTutorial”  。 
2. 选择“删除资源组”。 
3. 在“键入资源组名称”中输入 CreateSQLEndpointTutorial，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建：

* 虚拟网络和堡垒主机。
* 虚拟机。
* 具有专用终结点的 Azure SQL 服务器。

你使用虚拟机通过专用终结点安全测试了到 SQL 服务器的连接。

对于下一步，你可能还会对“与 Azure SQL 数据库建立专用连接的 Web 应用”体系结构场景感兴趣，该场景将虚拟网络以外的 Web 应用程序连接到数据库的专用终结点。
> [!div class="nextstepaction"]
> [与 Azure SQL 数据库建立专用连接的 Web 应用](/azure/architecture/example-scenario/private-web-app/private-web-app)