---
title: 教程：使用 Azure 专用终结点连接到 Azure Cosmos 帐户
titleSuffix: Azure Private Link
description: 在本教程中开始使用 Azure 专用终结点以私密方式连接到 Azure Cosmos 帐户。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: c7c9caf5bbfd842c635b0cb887540457f15c081f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "114297030"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>教程：使用 Azure 专用终结点连接到 Azure Cosmos 帐户

Azure 专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络和 Bastion 主机。
> * 创建虚拟机。
> * 创建采用专用终结点的 Cosmos DB 帐户。
> * 测试与 Cosmos DB 帐户专用终结点的连接。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

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
    | 资源组 | 选择“myResourceGroup” |
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

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>创建采用专用终结点的 Cosmos DB 帐户

在本部分中，创建 Cosmos DB 帐户并配置专用终结点。

1. 在左侧菜单中，选择“创建资源” > “数据库” > “Cosmos DB 帐户”，或在搜索框中搜索“Cosmos DB 帐户”   。

2. 在“创建 Cosmos DB 帐户”的“基本信息”选项卡中，输入或选择以下信息 ：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅。 |
    | 资源组 | 选择“myResourceGroup”。 |
    | **实例详细信息** |  |
    | 帐户名称 | 输入 mycosmosdb。 如果该名称不可用，请输入一个唯一的名称。 |
    | API | 选择“Core (SQL)”。 |
    | 位置 | 选择“美国东部”。 |
    | 容量模式 | 保留默认值“预配吞吐量”。 |
    | 应用免费层折扣 | 保留默认值“不应用”。 |
    | 异地冗余 | 保留默认值“禁用”。 |
    | 多区域写入 | 保留默认值“禁用”。 |
   
3. 选择“网络”选项卡，或选择“下一步: 网络”按钮。

4. 在“网络”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **网络连接** | |
    | 连接方法 | 选择“专用终结点”。 |
    | **配置防火墙** | |
    | 允许来自 Azure 门户的访问 | 保留默认值“允许”。 |
    | 允许来自我的 IP 的访问 | 保留默认值“拒绝”。 |

5. 在“专用终结点”中，选择“+ 添加”。

6. 在“创建专用终结点”中，输入或选择以下信息：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroup” |
    | 位置 | 选择“美国东部” |
    | “属性” | 输入 **myPrivateEndpoint** |
    | 目标子资源 | 保留默认值“Core (SQL)” |
    | **网络** |  |
    | 虚拟网络 | 选择 myVNet |
    | 子网 | 选择“mySubnet” |
    | **专用 DNS 集成** |
    | 与专用 DNS 区域集成 | 保留默认值“是” |
    | 专用 DNS 区域 | 保留默认值“(新建) privatelink.documents.azure.com” |

7. 选择“确定”  。

8. 选择“查看 + 创建”。

9. 选择“创建”  。

### <a name="add-a-database-and-a-container"></a>添加数据库和容器

1. 选择“转到资源”，或在 Azure 门户的左侧菜单中，选择“所有资源” > “mycosmosdb”  。

2. 在左侧菜单中，选择“数据资源浏览器”。

3. 在“数据资源管理器”窗口中，选择“新容器” 。

4. 在“添加容器”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 数据库 ID | 保留默认值“新建”。 </br> 在文本框中输入“mydatabaseid”。 |
    | 吞吐量 (400 - 100,000 RU/s) | 保留默认值“手动”。 </br> 在文本框中输入“400”。 |
    | 容器 ID | 输入“mycontainerid” |
    | 分区键 | 输入“/mykey” |

5. 选择“确定”  。

6. 在 CosmosDB 帐户的“设置”部分中，选择“密钥” 。

7. 选择“主连接字符串”上的复制。

## <a name="test-connectivity-to-private-endpoint"></a>测试到专用终结点的连接

在本部分中，你将使用在上一步骤中创建的虚拟机通过专用终结点连接到 Cosmos DB 帐户。

1. 在左侧导航窗格中选择“资源组”。

1. 选择“myResourceGroup”。

1. 选择“myVM”。

1. 在 **myVM** 的“概述”页上，选择“连接”，然后选择“堡垒”。

1. 选择蓝色的“使用堡垒”按钮。

1. 输入在创建虚拟机期间输入的用户名和密码。

1. 连接后，在服务器上打开 Windows PowerShell。

1. 输入 `nslookup <cosmosdb-account-name>.documents.azure.com` 并验证名称解析。 将 \<cosmosdb-account-name> 替换为你在前面步骤中创建的 Cosmos DB 帐户的名称。 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```
    将为 Cosmos DB 帐户名称返回专用 IP 地址 10.1.0.5。  此地址位于你之前创建的虚拟网络的子网中。
    
1. 从门户获取 Azure Cosmos DB 主连接字符串。 有效的连接字符串采用以下格式：
   
   对于 SQL API 帐户：`https://<accountName>.documents.azure.com:443/;AccountKey=<accountKey>;` 对于用于 MongoDB 的 Azure Cosmos DB API：`mongodb://<accountName>:<accountKey>@cdbmongo36.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false`

1. 在虚拟机上安装 [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

1. 安装“Microsoft Azure 存储资源管理器”后选择“完成”。   保持选中此复选框以打开应用程序。

1. 在“连接到 Azure 存储”屏幕中，选择“取消” 。

1. 在存储资源管理器中，右键单击“Cosmos DB 帐户”，然后选择“连接到 Cosmos DB” 。

1. 在“选择 API”下，保留默认值“SQL” 。

1. 在“连接字符串”下的框中，粘贴在前面步骤中复制的 Cosmos DB 帐户的连接字符串。

1. 选择“**下一页**”。

1. 在“连接摘要”中验证设置是否正确。  

1. 选择“连接”  。

1. 关闭到 **myVM** 的连接。


## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除虚拟网络、虚拟机和 Cosmos DB 帐户：

1. 从左侧菜单中，选择“资源组”。

2. 选择“myResourceGroup”。

3. 选择“删除资源组”。

4. 在“键入资源组名称”中输入“myResourceGroup”。 

5. 选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建：

* 虚拟网络和堡垒主机。
* 虚拟机。
* Cosmos DB 帐户。

了解如何创建专用链接服务：
> [!div class="nextstepaction"]
> [创建专用链接服务](create-private-link-service-portal.md)
