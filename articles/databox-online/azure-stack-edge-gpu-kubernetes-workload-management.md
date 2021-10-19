---
title: 了解 Azure Stack Edge Pro 设备上的 Kubernetes 工作负载管理 | Microsoft Docs
description: 介绍如何管理 Azure Stack Edge Pro 设备上的 Kubernetes 工作负载。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: a0b3a5d0cbbae9f25a845f79db35d1049f93f776
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710181"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 设备上的 Kubernetes 工作负载管理

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

在 Azure Stack Edge Pro 设备上，配置计算角色时，会创建 Kubernetes 群集。 创建 Kubernetes 群集后，便可以在 Pod 中的 Kubernetes 群集上部署容器化应用程序。 可以通过不同的方式在 Kubernetes 群集中部署工作负载。 

本文介绍可用于在 Azure Stack Edge Pro 设备上部署工作负载的各种方法。

## <a name="workload-types"></a>工作负载类型

可在 Azure Stack Edge Pro 设备上部署的常见工作负载有两种：无状态应用程序或有状态应用程序。

- **无状态应用程序** 不会保留自身状态，因此不会将任何数据保存到永久性存储中。 所有用户和会话数据会在客户端上保留。 无状态应用程序的一些例子包括 Web 前端（如 Nginx）和其他 Web 应用程序。

    可以创建用于在群集上部署无状态应用程序的 Kubernetes 部署。 

- **有状态应用程序** 需要保存其状态。 有状态应用程序使用永久性存储（例如永久卷）来保存数据，以供服务器或其他用户使用。 有状态应用程序的例子包括 [Azure SQL Edge](../azure-sql-edge/overview.md) 和 MongoDB 等数据库。

    可以创建 Kubernetes 部署来部署有状态应用程序。 

## <a name="deployment-flow"></a>部署流

若要在 Azure Stack Edge Pro 设备上部署应用程序，请执行以下步骤： 
 
1. **配置访问权限**：先使用 PowerShell 运行空间创建用户和命名空间，并向用户授予对该命名空间的访问权限。
2. **配置存储**：然后在 Azure 门户中使用 Azure Stack Edge 资源，通过对要部署的有状态应用程序使用静态或动态预配，来创建永久卷。
3. **配置网络**：最后使用服务在 Kubernetes 群集外部和内部公开应用程序。
 
## <a name="deployment-types"></a>部署类型

有三种主要方法可用于部署工作负载。 使用其中任一部署方法都能连接到设备上的不同命名空间，并部署无状态或有状态的应用程序。

![Kubernetes 工作负载部署](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **本地部署**：此部署通过 `kubectl` 等命令行访问工具实现，使用这类工具可以部署 Kubernetes `yamls`。 通过 `kubeconfig` 文件来访问 Azure Stack Edge Pro 上的 Kubernetes 群集。 有关详细信息，请转到[通过 Kubectl 访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。

- **IoT Edge 部署**：这是通过与 Azure IoT 中心连接的 IoT Edge 来实现的。 通过 `iotedge` 命名空间连接到 Azure Stack Edge Pro 设备上的 Kubernetes 群集。 此命名空间中部署的 IoT Edge 代理负责与 Azure 之间的连接。 通过使用 Azure DevOps CI/CD 来应用 `IoT Edge deployment.json` 配置。 命名空间和 IoT Edge 管理是通过云操作员来完成的。

- **启用了 Azure Arc 的 Kubernetes 部署**：启用了 Azure Arc 的 Kubernetes 是一种混合管理工具，可用于在 Kubernetes 群集上部署应用程序。 通过 `azure-arc namespace` 连接到 Azure Stack Edge Pro 设备上的 Kubernetes 群集。 此命名空间中部署的代理负责与 Azure 之间的连接。 通过使用基于 GitOps 的配置管理来应用部署配置。 
    
    通过启用了 Azure Arc 的 Kubernetes，还可以使用适用于容器的 Azure Monitor 来查看和监视群集。 有关详细信息，请转到[什么是启用了 Azure Arc 的 Kubernetes？](../azure-arc/kubernetes/overview.md)。
    
    已启用 Azure Arc 的 Kubernetes 将于 2021 年 3 月面向用户正式发布，并且将收取标准使用费用。 作为重要的预览版客户，你可以在 Azure Stack Edge 设备上免费使用启用了 Azure Arc 的 Kubernetes。 若要获得预览版产品/服务，请创建[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)：

    1. 在“问题类型”下，选择“计费”。  
    2. 在“订阅”下，选择自己的订阅。
    3. 在“服务”下选择“我的服务”，然后选择“Azure Stack Edge”。
    4. 在“资源”下，选择你的资源。
    5. 在“摘要”下，键入问题说明。
    6. 在“问题类型”下，选择“意外费用”。
    7. 在“问题子类型”下，选择“帮助我了解免费试用版的费用”。


## <a name="choose-the-deployment-type"></a>选择部署类型

在部署应用程序时，请考虑以下信息：

- **单个或多个类型**：可以选择一个部署选项，也可混合选用多个不同的部署选项。
- **云还是本地**：根据拥有的应用程序，可以选择通过 kubectl 实现本地部署或通过 IoT Edge 和 Azure Arc 实现云部署。 
    - 如果选择本地部署，只能使用部署了 Azure Stack Edge Pro 设备的网络。
    - 如果有可以部署的云代理，应部署云操作员并使用云管理。
- **IoT 还是 Azure Arc**：部署选择还取决于产品方案的目的。 如果要部署深度集成了 IoT 或 IoT 生态系统的应用程序或容器，请选择使用 IoT Edge 来部署应用程序。 如果有现有的 Kubernetes 部署，应首选 Azure Arc。


## <a name="next-steps"></a>后续步骤

若要通过 kubectl 在本地部署应用，请参阅：

- [通过 kubectl 在 Azure Stack Edge Pro 上部署无状态应用程序](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)。

若要通过 IoT Edge 部署应用，请参阅：

- [通过 IoT Edge 在 Azure Stack Edge Pro 上部署示例模块](azure-stack-edge-gpu-deploy-sample-module.md)。

若要通过 Azure Arc 部署应用，请参阅：

- [使用 Azure Arc 部署应用程序](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。
