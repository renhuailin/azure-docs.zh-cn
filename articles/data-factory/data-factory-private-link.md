---
title: 用于 Azure 数据工厂的 Azure 专用链接
description: 了解 Azure 专用链接在 Azure 数据工厂中的工作原理。
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/16/2021
ms.openlocfilehash: f4ae3d0653ce99be2017b1ef08ca0645c599ee10
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740989"
---
# <a name="azure-private-link-for-azure-data-factory"></a>用于 Azure 数据工厂的 Azure 专用链接

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

使用 Azure 专用链接，可以通过专用终结点连接到 Azure 中的各种平台即服务 (PaaS) 部署。 专用终结点是特定虚拟网络和子网中的专用 IP 地址。 有关支持专用链接功能的 PaaS 部署的列表，请参阅[专用链接文档](../private-link/index.yml)。 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>保护客户网络与 Azure 数据工厂之间的通信 
你可以将 Azure 虚拟网络设置为你的网络在云中的逻辑表示形式。 这样做可以有以下好处：
* 有助于保护 Azure 资源免受来自公共网络的攻击。
* 允许网络和数据工厂安全地相互通信。 

你还可以通过设置 Internet 协议安全性 (IPsec) VPN（站点到站点）连接或 Azure ExpressRoute（专用对等互连）连接，将本地网络连接到虚拟网络。 

你还可以在本地计算机上或虚拟网络中的虚拟机上安装自承载集成运行时。 这样做可以：
* 在云数据存储与专用网络中的数据存储之间运行复制活动。
* 针对本地网络或 Azure 虚拟网络中的计算资源调度转换活动。 

Azure 数据工厂与客户虚拟网络之间需要几个信道，如下表所示：

| 域 | 端口 | 说明 |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | 数据工厂进行创作和监视所需的控制平面。 |
| `*.{region}.datafactory.azure.net` | 443 | 自承载集成运行时连接到数据工厂服务时需要此端口。 |
| `*.servicebus.windows.net` | 443 | 自承载集成运行时需要用它来进行交互式创作。 |
| `download.microsoft.com` | 443 | 自承载集成运行时下载更新时需要此端口。 |

借助用于 Azure 数据工厂的 Azure 专用链接，你可以实现以下功能：
* 在虚拟网络中创建专用终结点。
* 启用与特定数据工厂实例的专用连接。 

与 Azure 数据工厂服务的通信通过专用链接进行，可提供安全的专用连接性。 

:::image type="content" source="./media/data-factory-private-link/private-link-architecture.png" alt-text="Azure 数据工厂的专用链接体系结构关系图。":::

为上述每个信道启用专用链接服务可提供以下功能：
- 支持：
   - 即使阻止了所有出站通信，依然可以在虚拟网络中创作和监视数据工厂。
   - 可以在专用网络环境中安全地执行自承载集成运行时与 Azure 数据工厂服务之间的命令通信。 自承载集成运行时与 Azure 数据工厂服务之间的流量通过专用链接传输。 
- 当前不支持：
   - 使用自承载集成运行时进行的交互式创作（如测试连接、浏览文件夹列表和表列表、获取架构和预览数据）通过专用链接进行。
   - 如果启用自动更新，则可从 Microsoft 下载中心自动下载自承载集成运行时的最新版本，本次不支持。

   > [!NOTE]
   > 对于当前不支持的功能，仍需要在虚拟网络或企业防火墙中配置前面提到的域和端口。 

   > [!NOTE]
   > 通过专用终结点连接到 Azure 数据工厂仅适用于数据工厂中的自承载集成运行时。 Azure Synapse 不支持此操作。

> [!WARNING]
> 如果你在 Azure 数据工厂中启用专用链接并同时阻止公共访问，请确保在创建链接服务时，凭据存储在 Azure 密钥保管库中。 否则，凭据将无法生效。

## <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改
创建专用终结点时，数据工厂的 DNS CNAME 资源记录将更新为子域中具有前缀“privatelink”的别名。 默认情况下，我们还会创建一个与 privatelink 子域对应的[专用 DNS 区域](../dns/private-dns-overview.md)，其中包含专用终结点的 DNS A 资源记录。

使用专用终结点从 VNet 外部解析数据工厂终结点 URL 时，它会解析为数据工厂服务的公共终结点。 从托管专用终结点的 VNet 进行解析时，存储终结点 URL 解析为专用终结点的 IP 地址。

对于上面所示的示例，数据工厂“DataFactoryA”的 DNS 资源记录在从托管专用终结点的 VNet 外部解析时将为：

| 名称 | 类型 | Value |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}.datafactory.azure.net | CNAME   | DataFactoryA.{region}.privatelink.datafactory.azure.net |
| DataFactoryA.{region}.privatelink.datafactory.azure.net | CNAME   | < 数据工厂服务公共终结点 > |
| < 数据工厂服务公共终结点 >  | A | < 数据工厂服务公共 IP 地址 > |

当 DataFactoryA 的 DNS 资源记录由托管专用终结点的 VNet 解析时，将为：

| 名称 | 类型 | Value |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}.datafactory.azure.net | CNAME   | DataFactoryA.{region}.privatelink.datafactory.azure.net |
| DataFactoryA.{region}.privatelink.datafactory.azure.net   | A | < private endpoint IP address > |

如果在网络上使用自定义 DNS 服务器，则客户端必须能够将数据工厂终结点的 FQDN 解析为专用终结点 IP 地址。 应配置 DNS 服务器以将专用链接子域委托到 VNet 的专用 DNS 区域，或者使用专用终结点 IP 地址为“DataFactoryA.{region}.privatelink.datafactory.azure.net”配置 A 记录。

有关配置自己的 DNS 服务器以支持专用终结点的详细信息，请参阅以下文章：
- [Azure 虚拟网络中资源的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [专用终结点的 DNS 配置](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-a-private-endpoint-link-for-azure-data-factory"></a>为 Azure 数据工厂设置专用终结点链接

你将在本节中为 Azure 数据工厂设置专用终结点链接。

你可以选择是否通过公共终结点或专用终结点将自承载集成运行时 (SHIR) 连接到 Azure 数据工厂，如下所示： 

:::image type="content" source="./media/data-factory-private-link/disable-public-access-shir.png" alt-text="阻止公开访问自承载集成运行时的屏幕截图。":::

在“网络”边栏选项卡上的“数据工厂门户”页中创建之后，你可以随时更改选择。  在其中启用专用终结点后，你还必须将专用终结点添加到数据工厂。

专用终结点需要用于链接的虚拟网络和子网，以及子网中的虚拟机，该虚拟机将用于运行自承载集成运行时 (SHIR)，通过专用终结点链接进行连接。

### <a name="create-the-virtual-network"></a>创建虚拟网络
如果你没有可与专用终结点链接一起使用的现有虚拟网络，则必须创建一个虚拟网络，并分配一个子网。  

1. 登录 Azure 门户：https://portal.azure.com。
2. 在屏幕的左上方选择“创建资源”>“网络”>“虚拟网络”，或者在搜索框中搜索“虚拟网络”。  

3. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 选择 Azure 订阅                                  |
    | 资源组   | 为你的虚拟网络选择一个资源组 |
    | **实例详细信息** |                                                                 |
    | 名称             | 输入虚拟网络的名称 |
    | 区域           | 重要提示：选择的区域要与专用终结点将使用的区域保持一致 |

4. 选择“IP 地址”选项卡  ，或选择页面底部的“下一步:  IP 地址”按钮。

5. 在“IP 地址”  选项卡上，输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | IPv4 地址空间 | 输入“10.1.0.0/16” |

6. 在“子网名称”下，选择词语“默认”。

7. 在“编辑子网”中输入以下信息： 

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | 子网名称 | 输入子网的名称 |
    | 子网地址范围 | 输入“10.1.0.0/24” |

8. 选择“保存” 。

9. 选择“查看 + 创建”  选项卡，或选择“查看 + 创建”  按钮。

10. 选择“创建”。

### <a name="create-a-virtual-machine-for-the-self-hosted-integration-runtime-shir"></a>为自承载集成运行时 (SHIR) 创建一个虚拟机
你还必须创建或分配现有虚拟机，以在上述新子网中运行自承载集成运行时。

1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”，或者在搜索框中搜索“虚拟机”。   
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择资源组 |
    | **实例详细信息** |  |
    | 虚拟机名称 | 为虚拟机输入名称 |
    | 区域 | 为你的虚拟网络选择上述用到的区域 |
    | 可用性选项 | 选择“无需基础结构冗余” |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen1”（或任何其他支持自承载集成运行时的 Windows 映像） |
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
    | 虚拟网络 | 选择上述创建的虚拟网络。 |
    | 子网 | 选择上述创建的子网。 |
    | 公共 IP | 选择“无”。 |
    | NIC 网络安全组 | **基本**|
    | 公共入站端口 | 选择“无”。 |
   
5. 选择“查看 + 创建”。 
  
6. 检查设置，然后选择“创建”。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="create-the-private-endpoint"></a>创建专用终结点 
最后，你必须在数据工厂中创建专用终结点。

1. 在你数据工厂的 Azure 门户页，选择“网络”边栏选项卡和“专用终结点连接”选项卡，然后选择“+ 专用终结点”。 

:::image type="content" source="./media/data-factory-private-link/create-private-endpoint.png" alt-text="用于创建专用终结点的专用终结点连接窗格的屏幕截图。":::

2. 在“创建专用终结点”的“基本信息”选项卡中，输入或选择以下信息： 

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅 |
    | 资源组 | 选择资源组 |
    | **实例详细信息** |  |
    | 名称  | 为你的终结点输入名称 |
    | 区域 | 选择上述创建的虚拟网络的区域 |

3. 选择“资源”选项卡或页面底部的“下一步: 资源”按钮。
    
4. 在“资源”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 连接方法 | 选择“连接到我的目录中的 Azure 资源” |
    | 订阅 | 选择订阅 |
    | 资源类型 | 选择“Microsoft.Datafactory/factories” |
    | 资源 | 选择数据工厂 |
    | 目标子资源 | 如果要在自承载集成运行时与 Azure 数据工厂服务之间使用命令通信所用的专用终结点，请选择“datafactory”作为目标子资源。 如果要在虚拟网络中使用创作和监视数据工厂所用的专用终结点，请选择“门户”作为目标子资源。|

5. 选择“配置”选项卡或屏幕底部的“下一步: 配置”按钮。

6. 在“配置”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **联网** |  |
    | 虚拟网络 | 选择上述创建的虚拟网络。 |
    | 子网 | 选择上述创建的子网。 |
    | **专用 DNS 集成** |  |
    | 与专用 DNS 区域集成 | 保留默认值“是”。 |
    | 订阅 | 选择订阅。 |
    | 专用 DNS 区域 | 保留默认值“(新建) privatelink.azurewebsites.net”。
    

7. 选择“查看 + 创建”。

8. 选择“创建”  。

> [!NOTE]
> 禁用公共网络访问仅适用于自承载集成运行时，不适用于 Azure Integration Runtime 和 SQL Server Integration Services (SSIS) Integration Runtime。

> [!NOTE]
> 在为门户创建专用终结点后，仍可通过公用网络访问 Azure 数据工厂门户。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 数据工厂 UI 创建数据工厂](quickstart-create-data-factory-portal.md)
- [Azure 数据工厂简介](introduction.md)
- [Azure 数据工厂中的视觉对象创作](author-visually.md)
