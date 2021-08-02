---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 06/09/2021
ms.openlocfilehash: 86ccf634a9c33d3e8cfb8efd97e94f322fd5127f
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987795"
---
### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>计算模块显示“未知”状态且无法使用

#### <a name="error-description"></a>错误说明

设备上的所有模块显示“未知”状态且无法使用。 “未知”状态在重启后仍然存在。<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>建议的解决方案

删除 IoT Edge 服务，然后重新部署模块。 有关详细信息，请参阅[删除 IoT Edge 服务](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#remove-iot-edge-service)。


### <a name="modules-show-as-running-but-are-not-working"></a>模块显示为正在运行，但不起作用

#### <a name="error-description"></a>错误说明

模块的运行时状态显示为正在运行，但未显示预期结果。 

这种情况可能是由不起作用的模块路由配置引起的，或者 `edgehub` 可能没有按预期方式路由消息。 可检查 `edgehub` 日志。 如果发现有一些错误（如未能连接到 IoT 中心服务），则最常见的原因是连接问题。 出现连接问题可能是由于 IoT 中心服务用作通信默认端口的 AMPQ 端口被阻止，或者 Web 代理服务器阻止了这些消息。

#### <a name="suggested-solution"></a>建议的解决方案

执行以下步骤：
1. 若要解决此错误，请转到设备的 IoT 中心资源，然后选择你的 Edge 设备。 
1. 转到“设置模块”>“运行时设置”。 
1. 添加 `Upstream protocol` 环境变量并为其分配值 `AMQPWS`。 在此情况下配置的消息经端口 443 通过 Websocket 发送。

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>模块显示为正在运行，但未分配 IP

#### <a name="error-description"></a>错误说明

模块的运行时状态显示为正在运行，但容器化应用没有分配 IP 地址。 

发生这种情况是因为你为 Kubernetes 外部服务 IP 提供的 IP 范围不足。 请扩展此范围，确保已部署的每个容器或 VM 均已覆盖。

#### <a name="suggested-solution"></a>建议的解决方案

在设备的本地 Web UI 中，执行以下步骤：
1. 转到“计算”页。 选择要为其启用计算网络的端口。 
1. 为“Kubernetes 外部服务 IP”输入静态的连续 IP 范围。 你需要一个 IP 用于 `edgehub` 服务。 此外，每个 IoT Edge 模块以及要部署的每个 VM 都需要一个 IP。 
1. 选择“应用”。 更改的 IP 范围应立即生效。

有关详细信息，请参阅[更改容器的外部服务 IP](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#change-external-service-ips-for-containers)。

### <a name="configure-static-ips-for-iot-edge-modules"></a>为 IoT Edge 模块配置静态 IP

#### <a name="problem-description"></a>问题描述

Kubernetes 向 Azure Stack Edge Pro GPU 设备上的每个 IoT Edge 模块分配动态 IP。 需要一种方法来为模块配置静态 Ip。

#### <a name="suggested-solution"></a>建议的解决方案

可以通过 K8s 实验性部分为 IoT Edge 模块指定固定 IP 地址，如下所述： 

```yaml
{
  "k8s-experimental": {
    "serviceOptions" : {
      "loadBalancerIP" : "100.23.201.78",
      "type" : "LoadBalancer"
    }
  }
}
```
### <a name="expose-kubernetes-service-as-cluster-ip-service-for-internal-communication"></a>将 Kubernetes 服务公开为用于内部通信的群集 IP 服务

#### <a name="problem-description"></a>问题描述

默认情况下，IoT 服务类型为负载均衡器，并且服务分配有面向外部的 IP 地址。 如果应用程序需要 Kubernetes 群集中的 Kubernetes Pod 来访问群集中的其他 Pod，你可能需要将该服务配置为群集 IP 服务，而不是负载均衡器服务。 有关详细信息，请参阅 [Azure Stack Edge Pro GPU 设备上的 Kubernetes 网络](../articles/databox-online/azure-stack-edge-gpu-kubernetes-networking.md)。

#### <a name="suggested-solution"></a>建议的解决方案

可以通过 K8s 实验性部分使用创建选项。 以下服务选项应适用于端口绑定。

```yaml
{
"k8s-experimental": {
  "serviceOptions" : {
    "type" : "ClusterIP"
    }
  }
}
```