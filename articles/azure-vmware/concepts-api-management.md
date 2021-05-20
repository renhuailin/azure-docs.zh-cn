---
title: 概念 - API 管理
description: 了解 API 管理如何保护在 Azure VMware 解决方案虚拟机 (VM) 上运行的 API
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 958cc52c48d1121a69dca2fc901289ad1ed671cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541957"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>API 管理用于发布和保护在基于 Azure VMware 解决方案的 VM 上运行的 API

通过 Microsoft Azure [API 管理](https://azure.microsoft.com/services/api-management/)，可以安全地发布到内部或外部使用者。  只有开发人员和高级 SKU 允许 Azure 虚拟网络集成发布在 Azure VMware 解决方案工作负载上运行的 API。  两个 SKU 都安全地启用 API 管理服务和后端之间的连接。 

>[!NOTE]
>开发人员 SKU 适用于开发和测试，而高级 SKU 适用于生产部署。

对于在 Azure VMware 解决方案虚拟机 (VM) 上和本地运行的后端服务，API 管理配置是相同的。 对于两种部署，当将后端服务器放置在 Azure VMware 解决方案的 NSX 负载均衡器后面时，API 管理将负载均衡器上的虚拟 IP (VIP) 配置为后端终结点。 


## <a name="external-deployment"></a>外部部署

外部部署使用公共终结点发布供外部用户使用的 API。 开发人员和 DevOps 工程师可以通过 Azure 门户或 PowerShell 以及 API 管理开发人员门户来管理 API。

外部部署关系图显示整个过程以及涉及的执行者（显示在顶部）。 执行者包括：

- 管理员：表示管理员或 DevOps 团队，该管理员通过 Azure 门户和自动化机制（如 PowerShell 或 Azure DevOps）来管理 Azure VMware 解决方案。

- 用户：表示公开的 API 的使用者，同时表示使用 API 的用户和服务。

流量流通过 API 管理实例，该实例抽象化插入到中心虚拟网络中的后端服务。 ExpressRoute 网关将流量路由到 ExpressRoute Global Reach 通道，并到达将传入流量分发到不同后端服务实例的 NSX 负载均衡器。

API 管理具有 Azure 公共 API，建议激活 Azure DDOS 防护服务。 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="外部部署 - Azure VMware 解决方案的 API 管理":::


## <a name="internal-deployment"></a>内部部署

内部部署发布供内部用户或系统使用的 API。 DevOps 团队和 API 开发人员使用与外部部署中相同的管理工具和开发人员门户。

内部部署可[通过 Azure 应用程序网关](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)创建 API 的公共和安全终结点来完成。  网关的功能用于创建支持不同方案的混合部署。  

* 使用面向内部使用者和外部使用者的相同 API 管理资源。

* 使用单个 API 管理资源，并向外部使用者提供定义的部分 API。

* 提供简单的方式让客户启用和禁用通过公共 Internet 访问 API 管理的功能。

下面的部署关系图显示了可以是内部使用者或外部使用者的使用者，每个类型访问相同的或不同的 API。

在内部部署中，向相同的 API 管理实例公开 API。 在 API 管理之前，通过激活 Azure Web 应用程序防火墙 (WAF) 功能来部署应用程序网关。 此外，还部署了一组 HTTP 侦听器和规则来筛选流量，仅公开在 Azure VMware 解决方案上运行的一部分后端服务。


* 内部流量通过 ExpressRoute 网关路由到 Azure 防火墙，然后直接或通过流量规则路由到 API 管理。   

* 外部流量通过应用程序网关进入 Azure，该应用程序网关将外部保护层用于 API 管理。


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="内部部署 - Azure VMware 解决方案的 API 管理" lightbox="media/api-management/internal-deployment.png":::