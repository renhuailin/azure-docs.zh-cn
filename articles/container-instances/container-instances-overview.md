---
title: Azure 中的无服务器容器
description: Azure 容器实例服务提供了在 Azure 中运行隔离容器的最简捷方式，既无需管理虚拟机，也不必采用更高级的业务流程协调程序。
ms.topic: overview
ms.date: 03/22/2021
ms.custom: seodec18, mvc
ms.openlocfilehash: 4574802b86e517eac5a908b03759dcca3f17d9b3
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292306"
---
# <a name="what-is-azure-container-instances"></a>什么是 Azure 容器实例？

容器正在变成对云应用程序执行打包、部署和管理操作的首选方式。 Azure 容器实例提供了在 Azure 中运行容器的最简捷方式，既无需管理任何虚拟机，也不必采用更高级的服务。

不管什么方案（包括简单应用程序、任务自动化、生成作业），只要能够在隔离容器中操作，就可以使用 Azure 容器实例这种解决方案。 对于需要完整容器业务流程的方案（包括跨多个容器的服务发现、自动缩放、协调式应用程序升级），建议使用 [Azure Kubernetes Service (AKS)](../aks/index.yml)。

## <a name="fast-startup-times"></a>启动时间短

与虚拟机 (VM) 相比，容器的启动优势明显。 Azure 容器实例可在数秒内启动 Azure 中的容器，且无需预配和管理 VM。

从 Docker Hub 引入 Linux 或 Windows 容器映像、专用 [Azure 容器注册表](../container-registry/index.yml)或其他基于云的 Docker 注册表。 请访问[常见问题解答](container-instances-faq.yml)，了解 ACI 支持哪些注册表。 Azure 容器实例会缓存几个常见的基础操作系统映像，有助于加快自定义应用程序映像的部署速度。

## <a name="container-access"></a>容器访问

使用 Azure 容器实例可以通过公共 IP 地址和完全限定的域名 (FQDN) 直接向 Internet 公开容器组。 创建容器实例时，可以指定自定义的 DNS 名称标签，以便应用程序可在 *customlabel*.*azureregion*.azurecontainer.io 上访问。

通过提供交互式 shell，Azure 容器实例还可以在正在运行的容器中执行命令，以帮助进行应用程序开发和故障排除。 访问通过 HTTPS 进行，并使用 TLS 来保护客户端连接。

> [!IMPORTANT]
> 从 2020 年 1 月 13 日开始，Azure 容器实例将要求服务器和应用程序的所有安全连接都使用 TLS 1.2。 对 TLS 1.0 和 1.1 的支持将停用。

## <a name="compliant-deployments"></a>合规部署

### <a name="hypervisor-level-security"></a>虚拟机监控程序级别的安全性

从历史上看，容器提供了应用程序依赖项隔离和资源调控功能，但不能认为其功能已强大到可以进行恶意的多租户使用。 Azure 容器实例保证容器中的应用程序像在 VM 中一样保持隔离状态。

### <a name="customer-data"></a>客户数据

ACI 服务存储确保容器组按预期运行所需的最少客户数据。 将客户数据存储到一个区域的功能目前仅适用于亚太地区的东南亚区域（新加坡）和巴西地区的巴西南部区域（圣保罗州）。 对于其他所有区域，客户数据存储在以下[地域](https://azure.microsoft.com/global-infrastructure/geographies/)。 若要了解详细信息，请与 Azure 支持人员联系。

## <a name="custom-sizes"></a>自定义大小

容器通常优化成只运行单个应用程序，但此类应用程序的具体需求可能差异很大。 Azure 容器实例允许确切地指定 CPU 核心数和内存量，因此可提供最佳的利用方式。 费用取决于具体请求并按秒计收，因此可以根据实际需求来严格控制花费。

对于计算密集型作业（如机器学习），Azure 容器实例可以安排 Linux 容器使用 NVIDIA Tesla [GPU 资源](container-instances-gpu.md)（预览版）。

## <a name="persistent-storage"></a>持久存储

为了通过 Azure 容器实例来检索和持久保存状态，我们提供由 Azure 存储支持的直接[装载 Azure 文件共享](./container-instances-volume-azure-files.md)功能。

## <a name="linux-and-windows-containers"></a>Linux 和 Windows 容器

Azure 容器实例可以使用同一 API 来计划 Windows 和 Linux 容器。 直接在创建[容器组](container-instances-container-groups.md)时指定 OS 类型。

某些功能当前仅限于 Linux 容器：

* 每个容器组多个容器
* 卷装载（[Azure 文件存储](container-instances-volume-azure-files.md)、[emptyDir](container-instances-volume-emptydir.md)、[GitRepo](container-instances-volume-gitrepo.md)、[机密](container-instances-volume-secret.md)）
* Azure Monitor 的[资源使用情况指标](container-instances-monitor.md)
* [虚拟网络部署](container-instances-vnet.md)
* [GPU 资源](container-instances-gpu.md)（预览版）

对于 Windows 容器部署，请基于常用 [Windows 基础映像](/azure/container-instances/container-instances-faq#what-windows-base-os-images-are-supported)使用映像。

## <a name="co-scheduled-groups"></a>共同计划组

Azure 容器实例支持对共享主机、本地网络、存储和生命周期的[多容器组](container-instances-container-groups.md)进行计划。 这样即可将主要应用程序容器与其他配角容器（例如日志记录分支）结合使用。

## <a name="virtual-network-deployment"></a>虚拟网络部署

Azure 容器实例允许[将容器实例部署到 Azure 虚拟网络中](container-instances-vnet.md)。 部署到虚拟网络的子网中时，容器实例可以与虚拟网络中的其他资源（包括本地资源）进行安全通信（通过 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)或 [ExpressRoute](../expressroute/expressroute-introduction.md)）。

## <a name="next-steps"></a>后续步骤

尝试按照快速入门指南，使用单个命令将容器部署到 Azure：

> [!div class="nextstepaction"]
> [Azure 容器实例快速入门](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/