---
title: 修改 FPGA 设备上 IoT Edge 模块，使其在 Azure Stack Edge Pro GPU 设备上运行
description: 描述现有 FPGA 设备上的现有 IoT Edge 模块在 Azure Stack Edge Pro GPU 设备上运行所需的修改。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/03/2021
ms.author: alkohli
ms.openlocfilehash: 6e9e1319b90ab859c63c022e478bc26c4b8aedeb
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550212"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上从 Azure Stack Edge Pro FPGA 设备运行现有 IoT Edge 模块

本文详细介绍了在 Azure Stack Edge Pro FPGA 上运行的基于 docker 的 IoT Edge 模块所需的更改，使其可以在 Azure Stack 边缘 Pro GPU 设备上基于 Kubernetes 的 IoT Edge 平台上运行。 

## <a name="about-iot-edge-implementation"></a>关于 IoT Edge 实现 

IoT Edge 实现在 Azure Stack Edge Pro FPGA 设备上与 Azure Stack Edge Pro GPU 设备上的实现不同。 对于 GPU 设备，Kubernetes 用作 IoT Edge 的托管平台。 FPGA 设备上的 IoT Edge 使用基于 docker 的平台。 基于 docker 的应用程序模型 IoT Edge 会自动转换为 Kubernetes 本机应用程序模型。 但是，可能仍然需要进行一些更改，因为仅支持 Kubernetes 应用程序模型的一小部分。

如果要将工作负荷从 FPGA 设备迁移到 GPU 设备，则需要更改现有 IoT Edge 模块，使其在 Kubernetes 平台上成功运行。 你可能需要以不同的方式指定你的存储、网络、资源使用情况和 web 代理要求。 

## <a name="storage"></a>存储

为 IoT Edge 模块指定存储时，请考虑以下信息。

- 使用卷装载指定 Kubernetes 上的容器存储。
- Kubernetes 上的部署不能具有绑定来关联持久性存储或主机路径。
    - 对于永久性存储，请使用 `Mounts` with type `volume` 。
    - 对于主机路径，请使用 `Mounts` with type `bind` 。
- 对于 Kubernetes 上的 IoT Edge，bind `Mounts` 仅适用于目录，而不适用于文件。

#### <a name="example---storage-via-volume-mounts"></a>示例-通过卷装载进行存储 

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
对于 Kubernetes 上 IoT Edge 的主机路径，此处显示了使用 `Mounts` 类型的示例 `bind` ：
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

对于在 Kubernetes 上运行 IoT Edge 的 GPU 设备，将使用卷装入来指定存储。 若要使用共享设置存储，的值应 `Mounts.Source` 为 GPU 设备上预配的 SMB 或 NFS 共享的名称。 `/home/input`是在容器中可访问的卷的路径。 下面是在 GPU 设备上使用的容器创建选项：

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

- `HostPort` 在群集内部和外部公开服务时，必须提供规范。
    - K8sExperimental 选项，用于限制仅向群集公开服务。
- 模块间通信需要 `HostPort` 规范，并使用映射端口 (连接，而不是使用容器公开端口) 。
- 主机网络可与一起使用 `dnsPolicy = ClusterFirstWithHostNet` ，因为所有容器 (尤其 `edgeHub`) 并不一定要在主机网络上运行。 <!--Need further clarifications on this one-->
- 添加 TCP 的端口映射，同一请求中的 UDP 不起作用。

#### <a name="example---external-access-to-modules"></a>示例-对模块的外部访问 

对于指定端口绑定的任何 IoT Edge 模块，将使用在设备的本地 UI 中指定的 Kubernetes 外部服务 IP 范围来分配 IP 地址。 在 docker 上的 IoT Edge 与在 Kubernetes 上 IoT Edge 之间没有任何更改，如以下示例中所示。  

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

但是，若要查询分配给模块的 IP 地址，可以使用 Kubernetes 仪表板，如 [获取服务或模块的 ip 地址](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)中所述。 

或者，你可以 [连接到设备的 PowerShell 接口](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) ，并使用 `iotedge` list 命令列出你设备上运行的所有模块。 [命令输出](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)还将指示与模块关联的外部 ip。


## <a name="resource-usage"></a>资源使用情况 

对于 GPU 设备上基于 Kubernetes 的 IoT Edge 设置，将以不同于 FPGA 设备的方式指定硬件加速、内存和 CPU 需求等资源。 

#### <a name="compute-acceleration-usage"></a>计算加速度使用情况

若要在 FPGA 上部署模块，请使用容器创建选项 <!--with Device Bindings--> 如以下配置所示： <!--not sure where are device bindings in this config--> 

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
    
对于 GPU，请使用资源请求规范而不是设备绑定，如下面的最小配置中所示。 请求 nvidia 资源而不是杀手，并且不需要指定 `wireserver` 。 

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

#### <a name="memory-and-cpu-usage"></a>内存和 CPU 使用率
 
若要设置内存和 CPU 使用情况，请在部分中为模块使用处理器限制 `k8s-experimental` 。 <!--can we verify if this is how we set limits of memory and CPU-->

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
内存和 CPU 规范并不是必需的，但通常是良好的做法。 如果 `requests` 未指定，则使用限制中设置的值作为所需的最小值。 

为模块使用共享内存还需要另一种方法。 例如，可以在实时视频分析和推理解决方案之间使用主机 IPC 模式来访问共享内存，如在 [Azure Stack Edge 上部署实时视频分析](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal)中所述。


## <a name="web-proxy"></a>Web 代理 

配置 web 代理时，请考虑以下信息：

如果在网络中配置了 web 代理，请在 `edgeHub` FPGA 设备上基于 docker 的 IoT Edge 安装程序上配置部署的以下环境变量：

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (，除非 web 代理允许 `Amqp` 流量) 

对于 GPU 设备上基于 Kubernetes 的 IoT Edge 设置，需要在部署过程中配置此附加变量：

- `no_proxy`： localhost

- Kubernetes 平台上的 IoT Edge 代理使用端口35000和35001。 请确保模块不在这些端口上运行，否则可能导致端口冲突。 

## <a name="other-differences"></a>其他差异

- **部署策略**：你可能需要将任何更新的部署行为更改为模块。 IoT Edge 模块的默认行为是滚动更新。 如果模块正在使用硬件加速或网络端口等资源，则此行为会阻止更新的模块重新启动。 当处理 GPU 设备的 Kubernetes 平台上的永久性卷时，此行为可能会产生意想不到的后果。 若要重写此默认行为，你可以 `Recreate` 在模块的部分中指定。 `k8s-experimental`

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **模块名称**：模块名称应遵循 Kubernetes 命名约定。 将这些模块移动到 Kubernetes 时，可能需要将在 IoT Edge 上运行的模块重命名 IoT Edge 为 Docker。 有关命名的详细信息，请参阅 [Kubernetes 命名约定](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)。
- **其他选项**： 
    - 某些在 FPGA 设备上工作的 docker create 选项在 GPU 设备上的 Kubernetes 环境中不起作用。 例如：，如– EntryPoint。<!--can we confirm what exactly is required here-->
    - 等环境变量 `:` 需要替换为 `__` 。
    - **容器** 为 Kubernetes pod 创建状态将导致 IoT 中心资源上某个模块的 **回退** 状态。 尽管此状态的原因有很多，但通常是因为在较低的网络带宽连接上请求大型容器映像。 当该 pod 处于此状态时，模块的状态在 IOT **中心中显示为 "** 正在运行"。


## <a name="next-steps"></a>后续步骤

- 详细了解如何 [将 GPU 配置为使用模块](azure-stack-edge-j-series-configure-gpu-modules.md)。
