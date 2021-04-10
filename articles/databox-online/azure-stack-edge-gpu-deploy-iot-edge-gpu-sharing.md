---
title: 在 Azure Stack Edge Pro GPU 设备上使用 GPU 共享部署 IoT Edge 工作负载
description: 介绍如何在 Azure Stack Edge Pro GPU 设备上通过 IoT Edge 部署 GPU 共享工作负载。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564588"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>在 Azure Stack Edge Pro 上使用 GPU 共享部署 IoT Edge 工作负载

本文介绍容器化工作负载如何在 Azure Stack Edge Pro GPU 设备上共享 GPU。 方法涉及启用多进程服务 (MPS) 然后通过 IoT Edge 部署指定 GPU 工作负载。 

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 你可以访问已[激活](azure-stack-edge-gpu-deploy-activate.md)并[配置了计算](azure-stack-edge-gpu-deploy-configure-compute.md)的 Azure Stack Edge Pro GPU 设备。 你具有 [Kubernetes API 终结点](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)，并且已将此终结点添加到要访问设备的客户端上的 `hosts` 文件。

1. 你可以使用[受支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)访问客户端系统。 如果使用 Windows 客户端，则系统应运行 PowerShell 5.0 或更高版本来访问设备。

1. 在本地系统将以下部署保存到 `json`。 你将使用此文件中的信息来运行 IoT Edge 部署。 此部署基于 Nvidia 公开提供的[简单 CUDA 容器](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers)。 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>验证 GPU 驱动程序、CUDA 版本

第一步是验证设备是否正在运行所需的 GPU 驱动程序和 CUDA 版本。

1. [连接到设备的 PowerShell 界面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

1. 运行以下命令：

    `Get-HcsGpuNvidiaSmi`

1. 在 Nvidia smi 输出中，记下设备上的 GPU 版本和 CUDA 版本。 如果正在运行 Azure Stack Edge 2102 软件，此版本将与以下驱动程序版本相对应：

    - GPU 驱动程序版本：460.32.03
    - CUDA 版本：11.2
    
    下面是示例输出：

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>  
    ```

1. 使此会话保持打开状态，因为你在本文中将使用它来查看 Nvidia smi 输出。


## <a name="deploy-without-context-sharing"></a>不含上下文共享的部署

现在，当多进程服务未运行且没有上下文共享时，可以在设备上部署应用程序。 通过 Azure 门户，在存在于设备上的 `iotedge` 命名空间中部署。

### <a name="create-user-in-iot-edge-namespace"></a>在 IoT Edge 命名空间中创建用户

首先，要创建一个连接到 `iotedge` 命名空间的用户。 IoT Edge 模块部署在 iotedge 命名空间中。 有关详细信息，请参阅[设备上的 Kubernetes 命名空间](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types)。

按照以下步骤创建用户，并向用户授予对 `iotedge` 命名空间的访问权限。 

1. [连接到设备的 PowerShell 界面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

1. 在 `iotedge` 命名空间中创建新用户。 运行以下命令：

    `New-HcsKubernetesUser -UserName <user name>`

    下面是示例输出：

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. 复制以纯文本格式显示的输出。 在本地计算机上用户配置文件的 `.kube` 文件夹（例如 `C:\Users\<username>\.kube`）中，将输出另存为 config 文件（不含扩展名）。 

1. 授权你创建的用户访问 `iotedge` 命名空间。 运行以下命令：

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    下面是示例输出：

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
有关详细说明，请参阅[通过 kubectl 在 Azure Stack Edge Pro GPU 设备上连接并管理 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac)。

### <a name="deploy-modules-via-portal"></a>通过门户部署模块

通过 Azure 门户部署 IoT Edge 模块。 你将部署用于运行 n-body 模拟的公开提供的 Nvidia CUDA 示例模块。 有关详细信息，请参阅 [N-body 模拟](https://physics.princeton.edu//~fpretori/Nbody/intro.htm)。

1. 确保 IoT Edge 服务在设备上运行。

    ![IoT Edge 服务正在运行。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. 在右窗格中，选择“IoT Edge”磁贴。 转到“IoT Edge”>“属性”。 在右窗格中，选择与你的设备关联的 IoT 中心资源。

    ![查看属性。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. 在 IoT 中心资源中，转到“自动设备管理”>“IoT Edge”。 在右窗格中，选择与你的设备关联的 IoT Edge 设备。

    ![转到“IoT Edge”。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. 选择“设置模块”。

    ![转到“设置模块”。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. 选择“+ 添加”>“IoT Edge 模块”。

    ![添加 IoT Edge 模块。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. 在“模块设置”选项卡上，提供 IoT Edge 模块名称和映像 URI。   将“映像请求策略”设置为“创建时”。 

    ![模块设置。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. 在“环境变量”选项卡上，将“NVIDIA_VISIBLE_DEVICES”指定为“0”。  

    ![环境变量。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. 在“容器创建选项”选项卡上，提供以下选项：

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    选项如下所示：

    ![容器创建选项。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    选择“添加”。

1. 添加的模块应显示为“正在运行”。 

    ![查看并创建部署。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. 重复添加第一个模块时所遵循的所有步骤，以添加模块。 在此示例中，提供 `cuda-sample2` 作为模块的名称。 

    ![第二个模块的模块设置。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    使用相同的环境变量，因为这两个模块将共享同一个 GPU。

    ![第二个模块的环境变量。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    使用为第一个模块提供的相同容器创建选项，然后选择“添加”。

    ![第二个模块的容器创建选项。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. 在“设置模块”页上，选择“查看 + 创建”，然后选择“创建”。   

    ![查看并创建第二个部署。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. 这两个模块的“运行时状态”现在应显示为“正在运行”。   

    ![第二个部署的状态。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>监视工作负载部署

1. 打开新的 PowerShell 会话。

1. 列出在 `iotedge` 命名空间中运行的 pod。 运行以下命令：

    `kubectl get pods -n iotedge`   

    下面是示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    设备上有两个 pod（`cuda-sample1-97c494d7f-lnmns` 和 `cuda-sample2-d9f6c4688-2rld9`）在设备上运行。

1. 尽管两个容器都运行 n-body 模拟，但请查看 Nvidia smi 输出中的 GPU 利用率。 转到设备的 PowerShell 界面并运行 `Get-HcsGpuNvidiaSmi`。

    下面是两个容器都运行 n-body 模拟时的示例输出：

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    可以看到，GPU 0 上有两个容器在 n-body 模拟下运行。 你还可以查看其相应的内存使用情况。

1. 完成模拟后，Nvidia smi 输出会显示设备上没有正在运行的进程。

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
 
1. 在 n-body 模拟完成后，请查看日志以了解部署的详细信息以及完成模拟所需的时间。 

    下面是第一个容器的示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    下面是第二个容器的示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. 停止模块部署。 在设备的 IoT 中心资源中：
    1. 转到“自动设备部署”>“IoT Edge”。 选择对应于你的设备的 IoT Edge 设备。

    1. 转到“设置模块”并选择模块。 

        ![选择“设置模块”。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. 在“设置模块”选项卡上选择模块。
    
        ![选择模块。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  在“模块设置”选项卡上，将“所需状态”设置为“已停止”。  选择“更新”。

        ![修改模块设置。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. 重复上述步骤，停止在设备上部署的第二个模块。 选择“查看 + 创建”，然后选择“创建” 。 这应会更新部署。

        ![查看并创建更新的部署。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. 多次刷新“设置模块”页。 直到模块“运行时状态”显示为“已停止”。 

        ![验证部署状态。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>通过上下文共享部署

现在，当 MPS 在设备上运行时，可以在两个 CUDA 容器上部署 n-body 模拟。 首先，在设备上启用 MPS。


1. [连接到设备的 PowerShell 界面](azure-stack-edge-gpu-connect-powershell-interface.md)。

1. 若要在设备上启用 MPS，请运行 `Start-HcsGpuMPS` 命令。

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. 从设备的 PowerShell 接口获取 Nvidia smi 输出。 可以看到 `nvidia-cuda-mps-server` 进程或 MPS 服务正在设备上运行。

    下面是示例输出：

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. 部署之前停止的模块。 通过“设置模块”将“所需状态”设置为“正在运行”。 

    下面是示例输出：

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    可以看到，已在设备上部署并运行模块。

1. 部署模块后，n-body 模拟也会开始在这两个容器上运行。 下面是在第一个容器上完成模拟后的示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    下面是在第二个容器上完成模拟后的示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. 当两个容器都运行 n-body 模拟时，从设备的 PowerShell 接口获取 Nvidia smi 输出。 下面是示例输出。 有三个进程，`nvidia-cuda-mps-server` 进程（C 类型）对应于 MPS 服务，`/tmp/nbody` 进程（M + C 类型）对应于模块部署的 n-body 工作负载。 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>后续步骤

- [在 Azure Stack Edge Pro 上部署共享 GPU Kubernetes 工作负载](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md)。
