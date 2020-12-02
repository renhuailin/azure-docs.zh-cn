---
title: Azure 数据工厂的 azure 专用链接
description: 了解 azure 专用链接在 Azure 数据工厂中的工作原理。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 8d28a1f2040cfec7b81081754a6abd3bc3e14439
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511468"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure 数据工厂的 azure 专用链接

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

使用 Azure 专用链接，可以通过专用终结点连接到各种平台即服务 (PaaS) Azure 中的部署。 专用终结点是特定虚拟网络和子网中的专用 IP 地址。 有关支持 Private Link 功能的 PaaS 部署的列表，请参阅 [私有链接文档](../private-link/index.yml)。 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>保护客户网络与 Azure 数据工厂之间的通信 
你可以在云中将 Azure 虚拟网络设置为网络的逻辑表示形式。 这样做具有以下优势：
* 可帮助保护 Azure 资源免受公共网络的攻击。
* 允许网络和数据工厂安全地相互通信。 

还可以通过设置 Internet 协议安全 (IPsec) VPN (站点到站点) 连接或 Azure ExpressRoute (专用对等互连) 连接，将本地网络连接到虚拟网络。 

你还可以在本地计算机或虚拟网络中的虚拟机上安装自承载集成运行时。 这样做可以：
* 在专用网络中的云数据存储和数据存储之间运行复制活动。
* 针对本地网络或 Azure 虚拟网络中的计算资源调度转换活动。 

Azure 数据工厂与客户虚拟网络之间需要几个信道，如下表所示：

| 域 | 端口 | 说明 |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | 数据工厂创作和监视所需的控制平面。 |
| `*.{region}.datafactory.azure.net` | 443 | 自承载集成运行时连接到数据工厂服务时需要此端口。 |
| `*.servicebus.windows.net` | 443 | 自承载集成运行时需要用它来进行交互式创作。 |
| `download.microsoft.com` | 443 | 自承载集成运行时下载更新时需要此端口。 |

由于支持 Azure 数据工厂的私有链接，你可以：
* 在虚拟网络中创建专用终结点。
* 启用与特定数据工厂实例的专用连接。 

与 Azure 数据工厂服务的通信将经历专用链接，并帮助提供安全的专用连接性。 不需要在虚拟网络或企业防火墙中配置前面的域和端口，以提供更安全的方式来保护资源。  

![Azure 数据工厂体系结构的专用链接关系图。](./media/data-factory-private-link/private-link-architecture.png)

为上述每个通信通道启用专用链接服务可提供以下功能：
- **支持**：
   - 即使阻止所有出站通信，也可以在虚拟网络中创作和监视数据工厂。
   - 可以在专用网络环境中安全地执行自承载集成运行时与 Azure 数据工厂服务之间的命令通信。 自承载集成运行时与 Azure 数据工厂服务之间的流量通过专用链接进行。 
- **当前不支持**：
   - 使用自承载集成运行时的交互式创作，如 "测试连接"、"浏览文件夹列表" 和 "表列表"、"获取架构" 和 "预览数据"，将通过 "专用" 链接。
   - 如果启用自动更新，则可以从 Microsoft 下载中心自动下载新版本的自承载集成运行时。

   > [!NOTE]
   > 对于当前不支持的功能，仍需要在虚拟网络或企业防火墙中配置前面提到的域和端口。 

> [!WARNING]
> 创建链接服务时，请确保你的凭据存储在 Azure 密钥保管库中。 否则，在 Azure 数据工厂中启用专用链接时，凭据将不起作用。

## <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改
创建专用终结点时，数据工厂的 DNS CNAME 资源记录将更新为具有前缀 "privatelink" 的子域中的别名。 默认情况下，我们还会创建一个 [专用 dns 区域](https://docs.microsoft.com/azure/dns/private-dns-overview)，该区域与 "privatelink" 子域相对应，其中 dns a 用于专用终结点的资源记录。

当你通过专用终结点将数据工厂终结点 URL 从 VNet 外部解析时，它将解析为数据工厂服务的公共终结点。 从托管专用终结点的 VNet 进行解析时，存储终结点 URL 解析为专用终结点的 IP 地址。

对于上面所示的示例，数据工厂 "DataFactoryA" 的 DNS 资源记录在托管专用终结点的 VNet 之外进行解析时将为：

| 名称 | 类型 | 值 |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}. datafactory | CNAME   | DataFactoryA.{region}. privatelink. datafactory |
| DataFactoryA.{region}. privatelink. datafactory | CNAME   | < 数据工厂服务公共终结点 > |
| < 数据工厂服务公共终结点 >  | A | < 数据工厂服务公共 IP 地址 > |

当在承载专用终结点的 VNet 中解析时，DataFactoryA 的 DNS 资源记录将为：

| 名称 | 类型 | 值 |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}. datafactory | CNAME   | DataFactoryA.{region}. privatelink. datafactory |
| DataFactoryA.{region}. privatelink. datafactory   | A | < 专用终结点 IP 地址 > |

如果在网络上使用自定义 DNS 服务器，则客户端必须能够将数据工厂端点的 FQDN 解析到专用终结点 IP 地址。 应将 DNS 服务器配置为将专用链接子域委托给 VNet 的专用 DNS 区域，或为 "DataFactoryA" 配置 A 记录。具有专用终结点 IP 地址的 {region}. privatelink. datafactory。

有关配置自己的 DNS 服务器以支持专用终结点的详细信息，请参阅以下文章：
- [Azure 虚拟网络中资源的名称解析](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [专用终结点的 DNS 配置](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>为 Azure 数据工厂设置专用链接
您可以使用 [Azure 门户](../private-link/create-private-endpoint-portal.md)创建专用终结点。

你还可以在 Azure 门户中中转到 Azure 数据工厂，并创建专用终结点，如下所示：

![用于创建专用终结点的 "专用终结点连接" 窗格的屏幕截图。](./media/data-factory-private-link/create-private-endpoint.png)


如果要阻止对 Azure 数据工厂的公共访问并只允许通过专用链接访问，请在 Azure 门户中禁用对 Azure 数据工厂的网络访问权限，如下所示：

![用于创建专用终结点的 "网络访问" 窗格的屏幕截图。](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> 禁用公共网络访问仅适用于自承载集成运行时，而不是 Azure Integration Runtime 和 SQL Server Integration Services) Integration Runtime 的 (SSIS。

> [!NOTE]
> 禁用公共网络访问后，仍可通过公共网络访问 Azure 数据工厂门户。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 数据工厂 UI 创建数据工厂](quickstart-create-data-factory-portal.md)
- [Azure 数据工厂简介](introduction.md)
- [Azure 数据工厂中的视觉对象创作](author-visually.md)