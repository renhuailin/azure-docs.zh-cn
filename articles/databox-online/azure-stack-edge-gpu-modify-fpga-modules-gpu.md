---
title: 修改 FPGA 设备上的 IoT Edge 模块，使其在 Azure Stack Edge Pro GPU 设备上运行
description: 介绍要使现有 FPGA 设备上的现有 IoT Edge 模块在 Azure Stack Edge Pro GPU 设备上运行需要进行哪些修改。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 660fbf7cc4dd28c800d8f49fd5d990c99f97c4c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442989"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上运行 Azure Stack Edge Pro FPGA 设备中的现有 IoT Edge 模块

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

本文详细介绍了要使在 Azure Stack Edge Pro FPGA 上运行的基于 docker 的 IoT Edge 模块在 Azure Stack Edge Pro GPU 设备上的基于 Kubernetes 的 IoT Edge 平台上运行需要进行哪些更改。 

## <a name="about-iot-edge-implementation"></a>IoT Edge 实现简介 

Azure Stack Edge Pro FPGA 设备上的 IoT Edge 实现与 Azure Stack Edge Pro GPU 设备上的实现不同。 对于 GPU 设备，Kubernetes 用作 IoT Edge 的托管平台。 FPGA 设备上的 IoT Edge 使用基于 docker 的平台。 IoT Edge 基于 docker 的应用程序模型会自动转换为 Kubernetes 原生应用程序模型。 但是，可能仍然需要进行一些更改，因为仅支持一小部分的 Kubernetes 应用程序模型。

如果要将工作负载从 FPGA 设备迁移到 GPU 设备，则需要对现有 IoT Edge 模块进行更改，使其在 Kubernetes 平台上成功运行。 你可能需要以不同的方式指定存储、网络、资源使用情况和 Web 代理要求。 

## <a name="storage"></a>存储

为 IoT Edge 模块指定存储时，请考虑以下信息。

- 使用卷装载指定 Kubernetes 上的容器存储。
- Kubernetes 上的部署不能具有针对关联的永久性存储或主机路径的绑定。
    - 对于永久性存储，请使用类型为 `volume` 的 `Mounts`。
    - 对于主机路径，请使用类型为 `bind` 的 `Mounts`。
- 对于 Kubernetes 上的 IoT Edge，通过 `Mounts` 进行绑定仅适用于目录，而不适用于文件。

#### <a name="example---storage-via-volume-mounts"></a>示例 - 通过卷装载进行存储 

对于 docker 上的 IoT Edge，将使用主机路径绑定将设备上的共享映射到容器中的路径。 下面是在 FPGA 设备上使用的容器创建选项：

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
对于 Kubernetes 上的 IoT Edge 的主机路径，此处显示了使用类型为 `bind` 的 `Mounts` 的示例：
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

对于在 Kubernetes 上运行 IoT Edge 的 GPU 设备，将使用卷装载来指定存储。 若要使用共享预配存储，`Mounts.Source` 的值将为之前在 GPU 设备上预配的 SMB 或 NFS 共享的名称。 `/home/input` 是可在容器中访问卷的路径。 下面是在 GPU 设备上使用的容器创建选项：

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>网络

为 IoT Edge 模块指定网络时，请考虑以下信息。 

- 需要 `HostPort` 规范才能在群集内外部公开服务。
    - K8sExperimental 选项用于将服务公开范围仅限于群集。
- 模块间通信需要 `HostPort` 规范以及使用映射端口（而非使用容器公开端口）的连接。
- 主机网络适用于 `dnsPolicy = ClusterFirstWithHostNet` 的情况，因此所有容器（尤其是 `edgeHub`）也不必都位于主机网络上。 <!--Need further clarifications on this one-->
- 添加 TCP 的端口映射时，同一请求中的 UDP 不起作用。

#### <a name="example---external-access-to-modules"></a>示例 - 对模块的外部访问 

对于指定端口绑定的任何 IoT Edge 模块，均使用 Kubernetes 外部服务 IP 范围（已在设备的本地 UI 中指定）分配 IP 地址。 docker 上的 IoT Edge 与 Kubernetes 上的 IoT Edge 之间的容器创建选项没有任何更改，如以下示例中所示。  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

但是，若要查询分配给模块的 IP 地址，可以使用 Kubernetes 仪表板，如[获取服务或模块的 IP 地址](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)中所述。 

或者，可以[连接到设备的 PowerShell 接口](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)并使用 `iotedge` 列表命令以列出在设备上运行的所有模块。 [命令输出](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)还将指示与模块关联的外部 IP。


## <a name="resource-usage"></a>资源使用情况 

通过 GPU 设备上基于 Kubernetes 的 IoT Edge 设置，将以不同于 FPGA 设备的方式指定硬件加速、内存和 CPU 要求等资源。 

#### <a name="compute-acceleration-usage"></a>计算加速使用情况

若要在 FPGA 上部署模块，请使用容器创建选项， <!--with Device Bindings--> 如以下配置所示： <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
对于 GPU，请使用资源请求规范而不是设备绑定，如下面的最小配置中所示。 请求 nvidia 资源而非 catapult，并且不需要指定 `wireserver`。 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>内存和 CPU 使用情况
 
若要设置内存和 CPU 使用情况，请对 `k8s-experimental` 部分中的模块使用处理器限制。 <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
内存和 CPU 规范并不是必需的，但通常是不错的做法。 如果未指定 `requests`，则在限制范围内设置的值将用作所需的最小值。 

为模块使用共享内存还需要采用另一种方法。 例如，可以使用主机 IPC 模式在实时视频分析和推理解决方案之间提供共享内存访问权限，如[在 Azure Stack Edge 上部署实时视频分析](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal)中所述。


## <a name="web-proxy"></a>Web 代理 

配置 Web 代理时，请考虑以下信息：

如果在网络中配置了 Web 代理，请在 FPGA 设备上基于 docker 的 IoT Edge 设置上为 `edgeHub` 部署配置以下环境变量：

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs`（除非 Web 代理允许 `Amqp` 流量）

对于 GPU 设备上基于 Kubernetes 的 IoT Edge 设置，需要在部署过程中配置此附加变量：

- `no_proxy`：localhost

- Kubernetes 平台上的 IoT Edge 代理使用端口 35000 和 35001。 请确保模块不会在这些端口上运行，否则可能导致端口冲突。 

## <a name="other-differences"></a>其他差异

- **部署策略**：你可能需要针对模块的任何更新更改部署行为。 IoT Edge 模块的默认行为是滚动更新。 如果模块正在使用硬件加速或网络端口等资源，则此行为会阻止更新的模块重启。 此行为可能会产生意想不到的影响，尤其是在处理 GPU 设备的 Kubernetes 平台上的永久性卷时。 若要替代此默认行为，可以在模块的 `k8s-experimental` 部分中指定 `Recreate`。

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **模块名称**：模块名称应遵循 Kubernetes 命名约定。 将在具有 Docker 的 IoT Edge 上运行的模块移动具有 Kubernetes 的 IoT Edge 时，需要重命名这些模块。 有关命名的详细信息，请参阅 [Kubernetes 命名约定](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)。
- **其他选项**： 
    - 某些在 FPGA 设备上可用的 docker 创建选项在 GPU 设备上的 Kubernetes 环境中将不可用。 例如 EntryPoint。<!--can we confirm what exactly is required here-->
    - `:` 之类的环境变量需要替换为 `__`。
    - Kubernetes Pod 的状态为“正在创建容器”时，会导致 IoT 中心资源上的模块呈现“回退”状态 。 尽管导致 Pod 处于此状态的原因有很多，但一个常见的原因是通过低网络宽带连接拉取大型容器映像。 当 Pod 处于此状态时，尽管模块刚启动，但该模块在 IoT 中心的状态显示为“回退”。


## <a name="next-steps"></a>后续步骤

- 详细了解如何[配置 GPU 以使用模块](azure-stack-edge-j-series-configure-gpu-modules.md)。
