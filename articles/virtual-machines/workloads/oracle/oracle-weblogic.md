---
title: 采用哪些解决方案在 Azure 虚拟机上运行 Oracle WebLogic Server
description: 了解如何在 Microsoft Azure 虚拟机上运行 Oracle WebLogic Server。
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 03/23/2021
ms.author: rezar
ms.openlocfilehash: 310b2576489a45bb6c04d91778eb74f4aa4becdd
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694819"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>采用哪些解决方案在 Azure 虚拟机上运行 Oracle WebLogic Server？

**适用于：** :heavy_check_mark: Linux VM 

本页介绍了在 Azure 虚拟机上运行 Oracle WebLogic Server (WLS) 的解决方案。 Oracle 和 Microsoft 共同开发了这些解决方案并提供支持服务。

你还可以在 Azure Kubernetes 服务上运行 WLS。 [此 Microsoft 文章](./weblogic-aks.md)介绍了执行此操作的解决方案。

WLS 是一台领先的 Java 应用程序服务器，可在全球范围内运行一些最为任务关键的企业 Java 应用程序。 WLS 构成了 Oracle 软件套件的中间件基础。 Oracle 和 Microsoft 致力于让 WLS 的客户能够在 Azure 这一领先的云平台上运行工作负荷，并实现灵活性。

Azure WLS 解决方案旨在尽可能轻松地将 Java 应用程序迁移到 Azure 虚拟机。 该解决方案会通过为最常见的云预配方案生成已部署资源来实现此目的。 该解决方案可自动预配虚拟网络、存储、Java、WLS 和 Linux 资源。 只需极少的工作量，就能安装好 WebLogic Server。 该解决方案可以实现以下目的：使用网络安全组设置安全性；使用 Azure 应用程序网关进行负载均衡；使用 Azure Active Directory 进行身份验证，使用 ELK 和具有 Oracle 一致性的分布式缓存进行集中式日志记录。 你还可以在 Oracle 云或 Azure 上自动连接到现有数据库，包括 Azure PostgreSQL、Azure SQL 和 Oracle DB。 

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="你可以使用 Azure 门户在 Azure 上部署 WebLogic 服务器":::

有四种产品/服务可用于满足不同的方案：[不含管理服务器的单节点](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)、[含管理服务器的单节点](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)、[群集](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)和[动态群集](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)。 产品/服务可供免费使用。 下面是这些产品/服务的相关介绍和链接。

这些产品/服务自带许可。 它们假设你已经从 Oracle 获得了适当的许可证，并且得到了在 Azure 中运行这些产品/服务的适当许可。

这些产品/服务通过基础映像（例如 Oracle Linux 7.6上的 WebLogic Server 14 和 JDK 11）支持一系列的操作系统、Java 和 WLS 版本。 这些基础映像还可在 Azure 上自行使用。 这些基础映像适用于需要复杂的自定义 Azure 部署的客户。 [Microsoft Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1)中提供有当前的基础映像集。

_若有兴趣与开发这些产品/服务的工程团队密切合作来实现你的迁移方案，请选择 [商城产品/服务概述页面](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ 上的“[联系我](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)”按钮。 计划经理、架构师和工程师很快就会回复你并开始密切协作。 在产品/服务处于积极开发阶段时，可以免费采用迁移方案进行协作。

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server 单节点

[此产品/服务](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)预配一个虚拟机，并在其上安装 WLS。 它不会创建域或启动管理服务器。 单节点产品/服务对于具有高度自定义的域配置的方案非常有用。

## <a name="oracle-weblogic-server-with-admin-server"></a>含管理服务器的 Oracle WebLogic Server

[此产品/服务](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)预配一个虚拟机，并在其上安装 WLS。 它会创建域并启动管理服务器。 这样你就可以管理域并立即开始进行应用程序部署了。

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server 群集

[此产品/服务](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)会创建 WLS 虚拟机的高度可用群集。 管理服务器和所有托管服务器默认启动。 这样你可以管理群集并立即开始使用高度可用的应用程序了。

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server 动态群集

[此产品/服务](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)会创建 WLS 虚拟机的高度可用且可缩放的动态群集。 管理服务器和所有托管服务器默认启动。

这些解决方案可以相对轻松地启用各种生产就绪部署体系结构。 你可以通过允许关注商业应用程序开发，以最高效的方式满足大多数迁移案例。

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Azure 上启用了复杂的 WebLogic 服务器部署":::

除了解决方案自动预配的功能之外，客户完全还可以灵活地进一步自定义部署。 除部署应用程序之外，客户很可能会将更多 Azure 资源与部署进行集成。 鼓励客户[与开发团队联系](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)，并提供有关进一步改进解决方案的反馈。

## <a name="next-steps"></a>后续步骤

了解 Azure 上的产品/服务。

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 单节点](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [含管理服务器的 Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 群集](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 动态群集](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
