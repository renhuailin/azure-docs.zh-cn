---
title: 采用哪些解决方案在 Azure Kubernetes 服务上运行 Oracle WebLogic Server
description: 了解如何在 Azure Kubernetes 服务上运行 Oracle WebLogic Server。
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: 8aae65432ad904b4964febb2e69774597c9b5ae1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688447"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>采用哪些解决方案在 Azure Kubernetes 服务上运行 Oracle WebLogic Server？

适用于：:heavy_check_mark: Linux VM 

本页介绍用于在 Azure Kubernetes (AKS) 服务上运行 Oracle WebLogic Server (WLS) 的解决方案。 这些解决方案由 Oracle 和 Microsoft 共同开发，并由它们提供支持。

也可在 Azure 虚拟机上运行 WebLogic Server。 [此 Microsoft 文章](./oracle-weblogic.md)介绍了执行此操作的解决方案。

WebLogic Server 是领先的 Java 应用程序服务器，可在全球范围内运行一些最为任务关键的企业 Java 应用程序。 WebLogic Server 构成了 Oracle 软件套件的中间件基础。 Oracle 和 Microsoft 致力于让 WebLogic Server 客户能够在 Azure 这一领先的云平台上运行工作负载并实现灵活性。

## <a name="wls-on-aks-certified-and-supported"></a>WLS 在 AKS 上获得认证和支持
WebLogic Server 已通过 Oracle 和 Microsoft 认证，可在 AKS 上正常运行。 AKS 上的 WebLogic Server 解决方案旨在尽可能轻松地在 Docker 和 Kubernetes 基础结构上运行容器化且协调的 Java 应用程序。 这些解决方案侧重于可靠性、可伸缩性、可管理性和企业支持。

WebLogic Server 群集完全可通过 WebLogic Kubernetes Operator（以下简称为“Operator”）在 Kubernetes 上运行。 Operator 遵循标准 Kubernetes Operator 模式。 它通过自动执行其他手动任务并添加额外的操作可靠性功能，简化了 WebLogic 域的管理和操作以及 Kubernetes 上的部署。 Operator 支持 Oracle WebLogic Server 12c、Oracle Fusion Middleware Infrastructure 12c 及更高版本。 我们已使用 Operator 对面向 WebLogic Server 12.2.1.3 和 12.2.1.4 的官方 Docker 映像进行了测试。 有关 Operator 的详细信息，请查看 [Oracle 的官方文档](https://oracle.github.io/weblogic-kubernetes-operator/)。

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>AKS 上的 WLS 指南、脚本和示例
除了对 AKS 上的 WebLogic Server 进行认证，Oracle 和 Microsoft 还就在 AKS 上运行 WebLogic Server 联合提供了详细说明、脚本和示例。 该指南可整合到 [Operator 文档](https://oracle.github.io/weblogic-kubernetes-operator/)的“Azure Kubernetes 服务示例”部分中。 该指南旨在使在 AKS 部署上的生产 WebLogic Server 尽可能简单。 本指南使用 Oracle 提供的官方 WebLogic Server Docker 映像。 故障转移通过 Kubernetes 持久性卷声明访问的 Azure 文件存储来实现。 使用“LoadBalancer”类型的 Kubernetes 服务进行预配时，支持使用 Azure 负载均衡器。 支持使用 Azure 容器注册表 (ACR) 在自定义 Docker 映像内部署 WLS 域。 通过该指南，可实现高度的配置和自定义。

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="可使用示例脚本在 AKS 上部署 WebLogic Server":::

解决方案包括将 WLS 域部署到 AKS 的两种方法。 可直接将域部署到 Kubernetes 持久性卷。 如果要迁移到 AKS，但仍想要使用管理控制台或 WebLogic 脚本工具 (WLST) 管理 WLS，则适合使用此部署选项。 通过此选项，还可在不采用 Docker 开发的情况下迁移到 AKS。 要将 WLS 域部署到 AKS，Kubernetes 更原生的方式是基于 Oracle 容器注册表中的官方 WLS 映像生成自定义 Docker 映像，将自定义映像发布到 ACR，然后使用 Operator 将域部署到 AKS。 通过解决方案中的此选项，还可在部署完成后通过 Kubernetes ConfigMaps 更新域。

将来，通过市场产品/服务将 Azure 虚拟机上的 Oracle WebLogic Server 解决方案镜像化，可实现进一步的易用性和 Azure 服务集成。

这些解决方案是“自带许可”方案。 它们假设你已经从 Oracle 获得了适当的许可证，并且得到了在 Azure 中运行这些产品/服务的适当许可。

如果有兴趣与开发这些解决方案的工程团队密切合作来实现你的迁移方案，请填写[此简短调查](https://aka.ms/wls-on-azure-survey)并提供联系人信息。 计划经理、架构师和工程师很快就会回复你并开始密切协作。 当解决方案处于初始积极开发阶段时，可以免费就迁移方案展开协作。

## <a name="deployment-architectures"></a>部署体系结构

用于在 Azure Kubernetes 服务上运行 Oracle WebLogic Server 的解决方案可相对轻松地实现广泛的生产就绪部署体系结构。

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="在 AKS 上启用了复杂的 WebLogic Server 部署":::

除了解决方案提供的功能外，客户还具有完全的灵活性，可进一步自定义其部署。 除部署应用程序之外，客户很可能会将更多 Azure 资源与部署进行集成。 建议客户在调查中提供有关进一步改进解决方案的反馈。

## <a name="next-steps"></a>后续步骤

探索在 Azure Kubernetes 服务上运行 Oracle WebLogic Server。

> [!div class="nextstepaction"]
> [有关在 AKS 上运行 WLS 的指南、脚本和示例](https://oracle.github.io/weblogic-kubernetes-operator/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes Operator](https://oracle.github.io/weblogic-kubernetes-operator/)
