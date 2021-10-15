---
title: 通过 Kubernetes 仪表板监视 Azure Stack Edge Pro 设备 | Microsoft Docs
description: 说明如何访问和使用 Kubernetes 仪表板来监视 Azure Stack Edge Pro 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/31/2021
ms.author: alkohli
ms.openlocfilehash: 30e46f9425f4015893c08b94382e87cfa93c8be8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363183"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>使用 Kubernetes 仪表板监视 Azure Stack Edge Pro GPU 设备

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文说明如何访问和使用 Kubernetes 仪表板来监视 Azure Stack Edge Pro GPU 设备。 要监视设备，可以使用 Azure 门户中的图表，查看 Kubernetes 仪表板，或者通过设备的 PowerShell 界面运行 `kubectl` 命令。 

本文仅重点介绍可在 Kubernetes 仪表板上执行的监视任务。

在本文中，学习如何：

> [!div class="checklist"]
>
> * 访问设备上的 Kubernetes 仪表板
> * 查看设备上部署的模块
> * 获取设备上部署的应用程序的 IP 地址
> * 查看设备上部署的模块的容器日志


## <a name="about-kubernetes-dashboard"></a>关于 Kubernetes 仪表板

Kubernetes 仪表板是一个基于 Web 的用户界面，可用于对容器化应用程序进行故障排除。 Kubernetes 仪表板是 Kubernetes `kubectl` 命令行的替代方案，它基于 UI。 有关详细信息，请参阅 [Kubernetes 仪表板](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)。 

在 Azure Stack Edge Pro 设备上，可以在只读模式下使用 Kubernetes 仪表板，以大致了解在 Azure Stack Edge Pro 设备上运行的应用程序，查看 Kubernetes 群集资源的状态，并查看设备上发生的任何错误。

## <a name="access-dashboard"></a>访问仪表板

Kubernetes 仪表板为只读仪表板，在 Kubernetes 主节点上的端口 31000 上运行。 请按照以下步骤访问仪表板： 

1. 在设备的本地 UI 中，转到“设备”，然后转到“设备终结点” 。 
1. 复制“Kubernetes 仪表板”终结点。 在客户端的 `C:\Windows\System32\Drivers\etc\hosts` 文件中创建一个 DNS 条目，以连接到 Kubernetes 仪表板。 

    `<IP address of the Kubernetes dashboard>    <Kubernetes dashboard endpoint suffix>` 
        
    ![为“Kubernetes 仪表板”终结点添加 DNS 条目](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/add-domain-name-service-entry-hosts-1.png) 

1. 在“Kubernetes 仪表板”终结点这一行中，选择“下载配置”。此操作下载的 `kubeconfig` 可允许你访问仪表板。 将 `config.json` 文件保存在本地系统上。   

1. 从本地 UI 下载 Kubernetes 仪表板证书。 
    1. 在设备的本地 UI 中，转到“证书”。
    1. 找到“Kubernetes 仪表板”终结点的条目。 在此条目的右侧，选择“下载”，下载客户端系统上要用于访问仪表板的证书。 

    ![下载“Kubernetes 仪表板”终结点证书](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/download-kubernetes-dashboard-endpoint-certificate-1.png)  

1. 将下载的证书安装在客户端上。 如果使用 Windows 客户端，请执行以下步骤： 
    1. 选择证书，然后在“证书导入向导”中选择“本地计算机”作为存储位置。 

        ![在客户端 1 上安装证书](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. 在本地计算机上受信任的根存储中安装证书。 

        ![在客户端 2 上安装证书](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 
1. 复制并使用 Kubernetes 仪表板 URL 在浏览器中打开仪表板。 在“Kubernetes 仪表板登录”页面上：
    
    1. 选择“kubeconfig”。 
    1. 选择省略号“…”。浏览并指向之前在本地系统上下载的 `kubeconfig`。 选择“登录”。
        ![浏览到 kubeconfig 文件](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. 现在可以在只读模式下查看 Azure Stack Edge Pro 设备的 Kubernetes 仪表板。

    ![“Kubernetes 仪表板”主页](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>查看模块状态

计算模块是已实现业务逻辑的容器。 可以使用仪表板来验证计算模块是否已成功部署在 Azure Stack Edge Pro 设备上。

要查看模块状态，请在仪表板上执行以下步骤：

1. 在仪表板的左侧窗格中，转到“命名空间”。 按用于显示 IoT Edge 模块的命名空间（在本例中为 iotedge）进行筛选。
1. 在左侧窗格中，转到“工作负载”>“部署”。
1. 右侧窗格中将显示设备上部署的所有模块。 在本例中，Azure Stack Edge Pro 上已部署 GettingStartedWithGPU 模块。 你可以看到该模块已部署。

    ![查看模块部署](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>获取服务或模块的 IP 地址

可以使用仪表板获取要在 Kubernetes 群集外部公开的服务或模块的 IP 地址。 

通过“计算网络设置”页面中设备的本地 Web UI 为这些外部服务分配 IP 范围。 部署 IoT Edge 模块后，可能需要获取分配给特定模块或服务的 IP 地址。 

若要获取 IP 地址，请在仪表板上执行以下步骤：

1. 在仪表板的左侧窗格中，转到“命名空间”。 按用于部署外部服务的命名空间（在本例中为 iotedge）进行筛选。
1. 在左侧窗格中，转到“发现和负载均衡”>“服务”。
1. 右侧窗格中将显示 Azure Stack Edge Pro 设备上的 `iotedge` 命名空间中运行的所有服务。

    ![获取外部服务的 IP](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>查看容器日志

在某些情况下，你需要查看容器日志。 可以使用仪表板获取在 Kubernetes 群集上部署的特定容器的日志。

若要查看容器日志，请在仪表板上执行以下步骤：

1. 在仪表板的左侧窗格中，转到“命名空间”。 按用于部署 IoT Edge 模块的命名空间（在本例中为 iotedge）进行筛选。
1. 在左侧窗格中，转到“工作负载”>“Pod”。
1. 右侧窗格中将显示设备上运行的所有 Pod。 标识运行要查看其日志的模块的容器。 选择所标识的 Pod 的纵向省略号，并从上下文菜单中选择“日志”。

    ![查看容器日志 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. 日志显示在仪表板中内置的日志查看器中。 你也可以下载该日志。

    ![查看容器日志 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>查看 CPU、内存使用情况

Azure Stack Edge Pro 设备的 Kubernetes 仪表板还具有一个[指标服务器加载项](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/)，用于聚合 Kubernetes 资源中的 CPU 和内存使用情况。
 
例如，可以查看所有命名空间中的部署所消耗的 CPU 和内存。 

![查看所有部署中的 CPU 和内存使用情况](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

你还可以按特定的命名空间进行筛选。 在下面的示例中，可以只查看 Azure Arc 部署消耗的 CPU 和内存。  

![查看 Azure Arc 部署的 CPU 和内存使用情况](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Kubernetes 指标服务器提供管道自动缩放功能，如[水平 Pod 自动缩放程序](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)。


## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure Monitor 进行监视](azure-stack-edge-gpu-enable-azure-monitor.md)。
了解如何[运行诊断和收集日志](azure-stack-edge-gpu-troubleshoot.md)
