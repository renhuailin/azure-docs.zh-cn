---
title: 通过 Windows PowerShell 界面连接和管理 Microsoft Azure Stack Edge Pro GPU 设备 | Microsoft Docs
description: 介绍如何通过 Windows PowerShell 界面连接并管理 Azure Stack Edge Pro GPU。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/10/2021
ms.author: alkohli
ms.openlocfilehash: a2a6b7b514cb95eb327235c59bb569ac66f4a5d0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670493"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>通过 Windows PowerShell 管理 Azure Stack Edge Pro GPU 设备

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU 解决方案可让你处理数据，并通过网络将数据发送到 Azure。 本文介绍了为 Azure Stack Edge Pro GPU 设备执行的一些配置和管理任务。 可以使用 Azure 门户、本地 Web UI 或 Windows PowerShell 界面来管理设备。

本文重点介绍如何连接到设备的 PowerShell 界面，以及使用此界面可以执行的任务。 


## <a name="connect-to-the-powershell-interface"></a>连接到 PowerShell 接口

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>创建支持包

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]


## <a name="view-device-information"></a>查看设备信息
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>查看 GPU 驱动程序信息

如果在设备上配置了计算角色，则还可以通过 PowerShell 界面获取 GPU 驱动程序信息。 

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用 `Get-HcsGpuNvidiaSmi` 获取设备的 GPU 驱动程序信息。

    以下示例显示了此 cmdlet 的用法：

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    请记下此 cmdlet 的示例输出中的驱动程序信息。

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>启用多进程服务 (MPS)

Nvidia GPU 上的多进程服务 (MPS) 提供下述机制：GPU 可供多个作业共享，而每个作业分配有某个百分比的 GPU 资源。 MPS 是 Azure Stack Edge Pro GPU 设备上的预览版功能。 若要在设备上启用 MPS，请执行以下步骤：

[!INCLUDE [Enable MPS](../../includes/azure-stack-edge-gateway-enable-mps.md)]

> [!NOTE]
> 在更新设备软件和 Kubernetes 群集时，不会为工作负载保留 MPS 设置。 需要重新启用 MPS。

## <a name="reset-your-device"></a>重置设备

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>获取计算日志

如果在设备上配置了计算角色，则还可以通过 PowerShell 界面获取计算日志。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用 `Get-AzureDataBoxEdgeComputeRoleLogs` 获取设备的计算日志。

    以下示例显示了此 cmdlet 的用法：

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    下面是用于该 cmdlet 的参数的说明：
    - `Path`：提供要在其中创建计算日志包的共享的网络路径。
    - `Credential`：提供网络共享的用户名。 运行此 cmdlet 时，需要提供共享密码。
    - `FullLogCollection`：此参数确保日志包中包含所有计算日志。 默认情况下，日志包仅包含一部分日志。


## <a name="change-kubernetes-pod-and-service-subnets"></a>更改 Kubernetes Pod 和服务子网

默认情况下，Azure Stack Edge 设备上的 Kubernetes 分别为 pod 和服务使用子网 172.27.0.0/16 和 172.28.0.0/16。 如果这些子网在网络中已被使用，你可以运行 `Set-HcsKubeClusterNetworkInfo` cmdlet 来更改这些子网。

最好是在从 Azure 门户配置计算之前执行此项配置，因为 Kubernetes 群集就是在此配置计算的步骤中创建的。

1. [连接到设备的 PowerShell 界面](#connect-to-the-powershell-interface)。
1. 在设备的 PowerShell 界面中，运行：

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    请将 \<subnet details\> 替换为要使用的子网范围。 

1. 运行此命令后，可以使用 `Get-HcsKubeClusterNetworkInfo` 命令来验证 pod 和服务子网是否已更改。

下面是此命令的示例输出。

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```

## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>调试与 IoT Edge 相关的 Kubernetes 问题

在开始之前，必须：

- 已配置计算网络。 请参阅[教程：为带有 GPU 的 Azure Stack Edge Pro 配置网络](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。
- 已在设备上配置计算角色。
    
在配置了计算角色的 Azure Stack Edge Pro GPU 设备上，可以使用两个不同的命令集对设备进行故障排除或监视。

- 使用 `iotedge` 命令。 这些命令适用于针对设备的基本操作。
- 使用 `kubectl` 命令。 这些命令适用于针对设备的更广泛操作。

若要执行上述任一命令集，需要[连接到 PowerShell 界面](#connect-to-the-powershell-interface)。

### <a name="use-iotedge-commands"></a>使用 `iotedge` 命令

若要查看可用命令的列表，请[连接到 PowerShell 界面](#connect-to-the-powershell-interface)并使用 `iotedge` 函数。

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

下表简要说明了适用于 `iotedge` 的命令：

|command  |说明 |
|---------|---------|
|`list`     | 列出模块         |
|`logs`     | 提取模块的日志        |
|`restart`     | 停止和重启模块         |

#### <a name="list-all-iot-edge-modules"></a>列出所有 IoT Edge 模块

若要列出设备上运行的所有模块，请使用 `iotedge list` 命令。

下面是此命令的示例输出。 此命令列出所有模块、关联的配置，以及与模块关联的外部 IP。 例如，可以通过 `https://10.128.44.244` 访问 **webserver** 应用。 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```
#### <a name="restart-modules"></a>重启模块

可使用 `list` 命令列出设备上运行的所有模块。 然后，确定要重启的模块的名称并对其使用 `restart` 命令。

下面是演示如何重启模块的示例输出。 根据模块运行时长的说明，可以看到 `cuda-sample1` 已重启。

```powershell
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  ----------- ------                                          ----------- -------
edgehub      Running Up 5 days   mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:308
                                                                                             81/TCP,8883:31753/TCP
iotedged     Running Up 7 days   azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days   nvidia/samples:nbody
edgeagent    Running Up 7 days   azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 1 days   nvidia/samples:nbody

[10.100.10.10]: PS>iotedge restart cuda-sample1
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION  CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  -----------  ------                                          ----------- -------
edgehub      Running Up 5 days    mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:30
                                                                                              881/TCP,8883:31753/TC
                                                                                              P
iotedged     Running Up 7 days    azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days    nvidia/samples:nbody
edgeagent    Running Up 7 days    azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 4 minutes nvidia/samples:nbody

[10.100.10.10]: PS>

```

#### <a name="get-module-logs"></a>获取模块日志

使用 `logs` 命令获取设备上运行的任何 IoT Edge 模块的日志。 

如果在创建容器映像或在拉取映像时出现错误，请运行 `logs edgeagent`。 `edgeagent` 是负责预配其他容器的 IoT Edge 运行时容器。 由于 `logs edgeagent` 转储所有日志，因此查看最近发生的错误的适当方法是使用 `--tail `0` 选项。 

下面是示例输出。

```powershell
[10.100.10.10]: PS>iotedge logs cuda-sample2 --tail 10
[10.100.10.10]: PS>iotedge logs edgeagent --tail 10
<6> 2021-02-25 00:52:54.828 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:52:54.829 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Plan execution ended for deployment 11
[10.100.10.10]: PS>
```
> [!NOTE]
> 在 Azure Stack Edge 上，Kubernetes 上的 IoT Edge 不支持 GetModuleLogs 或 UploadModuleLogs 等直接方法。
 
 
### <a name="use-kubectl-commands"></a>使用 kubectl 命令

在配置了计算角色的 Azure Stack Edge Pro GPU 设备上，所有 `kubectl` 命令均可用于对模块进行监视或故障排除。 若要查看可用命令的列表，请在命令窗口中运行 `kubectl --help`。

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

有关 `kubectl` 命令的完整列表，请参阅 [`kubectl` 速查表](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)。


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>获取在 Kubernetes 群集外部公开的服务或模块的 IP

若要获取在 Kubernetes 外部公开的负载均衡服务的 IP，请运行以下命令：

`kubectl get svc -n iotedge`

下面是在 Kubernetes 群集外部公开的所有服务或模块的示例输出。 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
“外部 IP”列中的 IP 地址对应于服务或模块的外部终结点。 还可以[在 Kubernetes 仪表板中获取外部 IP](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)。


#### <a name="to-check-if-module-deployed-successfully"></a>检查模块是否已成功部署

计算模块是实现了业务逻辑的容器。 一个 Kubernetes pod 可以包含多个运行的容器。 

若要检查是否已成功部署计算模块，请连接到设备的 PowerShell 界面。
运行 `get pods` 命令，然后检查容器（对应于计算模块）是否正在运行。

若要获取特定命名空间中运行的所有 pod 的列表，请运行以下命令：

`get pods -n <namespace>`

若要检查通过 IoT Edge 部署的模块，请运行以下命令：

`get pods -n iotedge`

下面是 `iotedge` 命名空间中运行的所有 pod 的示例输出。

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

“Status”列下的状态指示命名空间中的所有 pod 正在运行，“Ready”指示 pod 中部署的容器数。  在以上示例中，所有 pod 正在运行，部署在每个 pod 中的所有模块也正在运行。 

若要检查通过 Azure Arc 部署的模块，请运行以下命令：

`get pods -n azure-arc`

或者，可以[连接到 Kubernetes 仪表板以查看 IoT Edge 或 Azure Arc 部署](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status)。


要获取给定命名空间的特定 pod 的更详细输出，可运行以下命令：

`kubectl describe pod <pod name> -n <namespace>` 

下面显示了示例输出。

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>获取容器日志

若要获取模块的日志，请在设备的 PowerShell 界面中运行以下命令：

`kubectl logs <pod_name> -n <namespace> --all-containers` 

由于 `all-containers` 标志将转储所有容器的所有日志，因此查看最近发生的错误的适当方法是使用选项 `--tail 10`。

下面是示例输出。 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

### <a name="change-memory-processor-limits-for-kubernetes-worker-node"></a>更改 Kubernetes 工作器节点的内存或处理器限制

若要更改 Kubernetes 工作器节点的内存或处理器限制，请执行以下步骤：

1. [连接到设备的 PowerShell 界面](#connect-to-the-powershell-interface)。
1. 若要获取工作器节点和角色选项的当前资源，请运行以下命令：

    `Get-AzureDataBoxEdgeRole`

    下面是示例输出。 请注意 `Resources` 节下的 `Name` 和 `Compute` 值。 `MemoryInBytes` 和 `ProcessorCount` 表示当前为 Kubernetes 工作器节点分配的内存和处理器计数值。  

    ```powershell
    [10.100.10.10]: PS>Get-AzureDataBoxEdgeRole
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:12
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True    
    [10.100.10.10]: PS>
    ```
    
1. 若要更改工作器节点的内存和处理器值，请运行以下命令：

   ```powershell
   Set-AzureDataBoxEdgeRoleCompute -Name <Name value from the output of Get-AzureDataBoxEdgeRole> -Memory <Value in Bytes> -ProcessorCount <No. of cores>
   ```

   下面是示例输出。 
    
    ```powershell
    [10.100.10.10]: PS>Set-AzureDataBoxEdgeRoleCompute -Name IotRole -MemoryInBytes 32GB -ProcessorCount 16
    
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:16
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
    
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True
    
    [10.100.10.10]: PS>    
    ```

更改内存和处理器使用率时，请遵循以下指导原则。

- 默认内存为设备规格的 25%。
- 默认处理器计数为设备规格的 30%。
- 更改内存和处理器计数值时，我们建议在设备内存和处理器计数的 15% 到 60% 之间改变值。 
- 建议的上限为 60%，以便为系统组件提供足够的资源。 

## <a name="connect-to-bmc"></a>连接到 BMC

基板管理控制器 (BMC) 用于远程监视和管理设备。 本部分介绍可用于管理 BMC 配置的 cmdlet。 在运行其中的任何 cmdlet 之前，请[连接到设备的 PowerShell 界面](#connect-to-the-powershell-interface)。

- `Get-HcsNetBmcInterface`：使用此 cmdlet 可以获取 BMC 的网络配置属性，例如 `IPv4Address`、`IPv4Gateway`、`IPv4SubnetMask` 和 `DhcpEnabled`。 
    
    下面是示例输出：
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`：可通过以下两种方式使用此 cmdlet。

    - 使用该 cmdlet 并通过对 `UseDhcp` 参数使用适当的值，为 BMC 启用或禁用 DHCP 配置。 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        下面是示例输出： 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - 使用此 cmdlet 可以配置 BMC 的静态配置。 可以指定 `IPv4Address`、`IPv4Gateway` 和 `IPv4SubnetMask` 的值。 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>&quot; -IPv4Gateway &quot;<IPv4 address of the gateway>&quot; -IPv4SubnetMask &quot;<IPv4 address for the subnet mask>"
        ```        
        
        下面是示例输出： 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`：使用此 cmdlet 可以修改 `EdgeUser` 的 BMC 密码。 用户名 `EdgeUser` 区分大小写。

    下面是示例输出： 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>退出远程会话

若要退出远程 PowerShell 会话，请关闭 PowerShell 窗口。

## <a name="next-steps"></a>后续步骤

- 在 Azure 门户中部署 [Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md)。
