---
title: 什么是在 Azure Kubernetes Service 上运行 Oracle WebLogic Server 的解决方案
description: 了解如何在 Azure Kubernetes 服务上运行 Oracle WebLogic Server。
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: ac9f81fbde33bdd10bc8374a566a4f2ba83fc253
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669014"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>什么是在 Azure Kubernetes Service 上运行 Oracle WebLogic Server 的解决方案？

本页介绍了在 Azure Kubernetes 服务 (AKS) 上运行 Oracle WebLogic Server (WLS) 的解决方案。 Oracle 和 Microsoft 共同开发并支持这些解决方案。

也可以在 Azure 虚拟机上运行 WebLogic 服务器。 [此 Microsoft 文章](./oracle-weblogic.md)介绍了执行此操作的解决方案。

WebLogic Server 是领先的 Java 应用程序服务器，可在全球范围内运行一些最重要的关键企业 Java 应用程序。 WebLogic Server 构成了 Oracle 软件套件的中间件基础。 Oracle 和 Microsoft 致力于让 WebLogic 服务器客户在 Azure 上运行工作负荷作为领先的云平台，从而实现选择和灵活性。

## <a name="wls-on-aks-certified-and-supported"></a>WLS on AKS 认证和支持
WebLogic 服务器已通过 Oracle 和 Microsoft 认证，可在 AKS 上正常运行。 AKS 解决方案上的 WebLogic 服务器旨在使其尽可能轻松地在 Docker 和 Kubernetes 基础结构上运行容器化的 Java 应用程序并进行协调。 这些解决方案侧重于可靠性、可伸缩性、可管理性和企业支持。

WebLogic 服务器群集完全启用为通过 WebLogic Kubernetes 运算符在 Kubernetes 上运行， (称为 "运算符"，) 。 运算符遵循标准 Kubernetes 运算符模式。 它通过自动执行手动任务并增加额外的操作可靠性功能，简化了 WebLogic 域和 Kubernetes 上部署的管理和操作。 操作员支持 Oracle WebLogic Server 12c、Oracle 合成中间件基础结构12c 和更高版本。 我们已测试了适用于 WebLogic Server 12.2.1.3 和12.2.1.4 的官方 Docker 映像和运算符。 有关操作员的详细信息，请参阅 [Oracle 的官方文档](https://oracle.github.io/weblogic-kubernetes-operator/)。

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>AKS 上的 WLS 指南、脚本和示例
除了在 AKS 上验证 WebLogic Server 外，Oracle 和 Microsoft 还提供了有关在 AKS 上运行 WebLogic 服务器的详细说明、脚本和示例。 本指南包含在 [操作员文档](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)的 "Azure Kubernetes 服务示例" 部分。 本指南旨在使生产 WebLogic 服务器在 AKS 部署上尽可能简单。 本指南使用 Oracle 提供的官方 WebLogic 服务器 Docker 映像。 故障转移通过 Kubernetes 永久性卷声明访问的 Azure 文件来实现。 使用 "LoadBalancer" 类型的 Kubernetes 服务进行预配时，支持 Azure 负载平衡器。 支持 Azure 容器注册表 (ACR) 在自定义 Docker 映像中部署 WLS 域。 本指南允许进行高度的配置和自定义。

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="可以使用示例脚本在 AKS 上部署 WebLogic 服务器":::

解决方案包括两种将 WLS 域部署到 AKS 的方法。 域可直接部署到 Kubernetes 永久性卷。 如果要迁移到 AKS，但仍想要使用管理控制台或 WebLogic 脚本编写工具 (WLST) 来迁移到，则此部署选项是正确的。 此选项还允许你迁移到 AKS，而无需采用 Docker 开发。 将 WLS 域部署到 AKS 的 Kubernetes 本机方式越多，就是基于 Oracle 容器注册表中的官方 WLS 映像构建自定义 Docker 映像，将自定义映像发布到 ACR，并使用运算符将该域部署到 AKS。 解决方案中的此选项还允许你在部署完成后通过 Kubernetes ConfigMaps 更新域。

将来可以通过 Azure 虚拟机解决方案中的 Oracle WebLogic Server 镜像，进一步使用和 Azure 服务集成。

_这些解决方案是自带许可证_。 它们假设你已有合适的 Oracle 许可证，并获得在 Azure 中运行产品/服务的适当许可。

_如果你希望与开发这些解决方案的工程团队密切合作迁移方案，请填写 [此简短调查](https://aka.ms/wls-on-azure-survey) ，并提供联系信息_。 计划经理、架构师和工程师将尽快进入你，并开始密切合作。 在解决方案处于主动初始开发阶段时，可以自由地在迁移方案中进行协作。

## <a name="deployment-architectures"></a>部署体系结构

在 Azure Kubernetes Service 上运行 Oracle WebLogic Server 的解决方案可以相对轻松地启用各种生产就绪部署体系结构。

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="在 AKS 上启用了复杂的 WebLogic 服务器部署":::

除了解决方案为客户提供更大的灵活性外，还可以进一步自定义部署。 部署应用程序很可能会在部署应用程序时将更多 Azure 资源与部署进行集成。 建议客户在调查中提供反馈，进一步改善解决方案。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure Kubernetes 服务上运行 Oracle WebLogic Server。

> [!div class="nextstepaction"]
> [在 AKS 上运行 WLS 的指南、脚本和示例](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes 运算符](https://oracle.github.io/weblogic-kubernetes-operator/)
