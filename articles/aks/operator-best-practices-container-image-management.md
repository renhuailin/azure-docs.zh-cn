---
title: 操作员最佳做法 - Azure Kubernetes 服务 (AKS) 中的容器映像管理
description: 了解有关如何在 Azure Kubernetes 服务 (AKS) 中管理和保护容器映像的群集操作员最佳做法
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 998d8602b6aa0e71a04f75aff1c29551ba09c8a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105113"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中容器映像管理和安全性的最佳做法

在 Azure Kubernetes Service (AKS) 中开发和运行应用程序时，容器和容器映像的安全性是重中之重。 包含过时基础映像或未修补的应用程序运行时的容器可能会引入安全风险以及可能的攻击途径。 

在生成和运行时，通过在容器中集成和运行扫描及修正工具可最大程度降低风险。 越早发现漏洞或过时的基础映像，群集就越安全。 

本文中的“容器”指以下两者：
* 存储在容器注册表中的容器映像。
* 正在运行的容器。

本文重点介绍如何保护 AKS 中的容器。 学习如何：

> [!div class="checklist"]
> * 扫描并修正映像漏洞。
> * 在更新基础映像时自动触发并重新部署容器映像。

还可以阅读[群集安全性][best-practices-cluster-security]和 [Pod 安全性][best-practices-pod-security]的最佳做法。

还可以使用[安全中心的容器安全性][security-center-containers]来扫描容器以发现漏洞。 [Azure 容器注册表][security-center-acr]与安全中心集成有助于防止映像和注册表会受到漏洞影响。

## <a name="secure-the-images-and-run-time"></a>保护映像和运行时

> **最佳实践指南** 
>
> 扫描容器映像的漏洞。 仅部署已验证的映像。 定期更新基础映像和应用程序运行时。 在 AKS 群集中重新部署工作负荷。

采用基于容器的工作负载时，需要验证用于构建应用程序的映像和运行时的安全性。 如何避免将安全漏洞引入部署？ 
* 在部署工作流中包含一个使用 [Twistlock][twistlock] 或 [Aqua][aqua] 等工具扫描容器映像的过程。
* 仅允许部署已验证的映像。

![扫描并修复容器映像、验证和部署](media/operator-best-practices-container-security/scan-container-images-simplified.png)

例如，你可以使用持续集成和连续部署 (CI/CD) 管道自动执行映像扫描、验证和部署。 Azure 容器注册表包含这些漏洞扫描功能。

## <a name="automatically-build-new-images-on-base-image-update"></a>更新基础映像时自动生成新映像

> **最佳实践指南** 
>
> 如果为应用程序映像使用的是基础映像，请在更新基础映像时通过自动化生成新映像。 由于更新的基础映像通常包含安全修补程序，请更新所有下游应用程序容器映像。

每次更新基础映像时，你还要更新所有下游容器映像。 将此生成过程集成到验证和部署管道中，例如，[Azure Pipelines][azure-pipelines] 或 Jenkins。 这些管道可以确保应用程序在更新的基础映像上继续运行。 验证应用程序容器映像后，就可以更新 AKS 部署，以便运行最新的安全映像。

Azure 容器注册表任务也可以在更新基础映像时自动更新容器映像。 凭借此功能，您可以生成一些基础映像并使用 Bug 和安全修补程序不断更新这些映像。

有关基础映像更新的详细信息，请参阅[使用 Azure 容器注册表任务在基础映像更新时自动化映像生成][acr-base-image-update].

## <a name="next-steps"></a>后续步骤

本文重点介绍了如何保护容器。 若要实施其中某些做法，请参阅以下文章：

* [使用 Azure 容器注册表任务在基础映像更新时自动化映像生成][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md