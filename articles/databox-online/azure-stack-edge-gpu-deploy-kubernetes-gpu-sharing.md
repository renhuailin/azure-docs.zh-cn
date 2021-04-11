---
title: 在 Azure Stack Edge Pro GPU 设备上部署使用 GPU 共享的 Kubernetes 工作负载
description: 介绍如何在 Azure Stack Edge Pro GPU 设备上通过 Kubernetes 部署 GPU 共享工作负载。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 04299ba4028de313f640074ca98c0b611f734981
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564586"
---
# <a name="deploy-a-kubernetes-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>在 Azure Stack Edge Pro 上部署使用 GPU 共享的 Kubernetes 工作负载

本文介绍容器化工作负载如何在 Azure Stack Edge Pro GPU 设备上共享 GPU。 在本文中你将运行两个作业，其中一个作业是在未启用 GPU 上下文共享的情况下运行的，另一个作业是在通过设备上的多进程服务 (MPS) 启用了上下文共享的情况下运行的。 有关详细信息，请参阅[多处理服务](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 可以访问已[激活](azure-stack-edge-gpu-deploy-activate.md)并[配置了计算](azure-stack-edge-gpu-deploy-configure-compute.md)的 Azure Stack Edge Pro GPU 设备。 具有 [Kubernetes API 终结点](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)，并且已将此终结点添加到要访问设备的客户端上的 `hosts` 文件。

1. 你可以使用[受支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)访问客户端系统。 如果使用 Windows 客户端，则系统应运行 PowerShell 5.0 或更高版本来访问设备。

1. 已创建命名空间和用户。 此外，已向用户授予对此命名空间的访问权限。 已在用于访问设备的客户端系统上安装了此命名空间的 kubeconfig 文件。 有关详细说明，请参阅[通过 kubectl 在 Azure Stack Edge Pro GPU 设备上连接并管理 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac)。 

1. 在本地系统上保存以下部署 `yaml` 文件。 稍后将使用此文件来运行 Kubernetes 部署。 此部署基于 Nvidia 公开提供的[简单 CUDA 容器](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers)。 

    ```yml
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: cuda-sample1
    spec:
      template:
        spec:
          hostPID: true
          hostIPC: true
          containers:
            - name: cuda-sample-container1
              image: nvidia/samples:nbody
              command: ["/tmp/nbody"]
              args: ["-benchmark", "-i=1000"]
              env:
              - name: NVIDIA_VISIBLE_DEVICES
                value: "0"
          restartPolicy: "Never"
      backoffLimit: 1
    ---
    
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: cuda-sample2
    spec:
      template:
        metadata:
        spec:
          hostPID: true
          hostIPC: true
          containers:
            - name: cuda-sample-container2
              image: nvidia/samples:nbody
              command: ["/tmp/nbody"]
              args: ["-benchmark", "-i=1000"]
              env:
              - name: NVIDIA_VISIBLE_DEVICES
                value: "0"
          restartPolicy: "Never"
      backoffLimit: 1
    ```

## <a name="verify-gpu-driver-cuda-version"></a>验证 GPU 驱动程序、CUDA 版本

第一步是验证设备是否正在运行所需的 GPU 驱动程序和 CUDA 版本。

1. [连接到设备的 PowerShell 界面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

1. 运行以下命令：

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```

1. 在 Nvidia smi 输出中，记下设备上的 GPU 版本和 CUDA 版本。 如果正在运行 Azure Stack Edge 2102 软件，此版本将与以下驱动程序版本相对应：

    - GPU 驱动程序版本：460.32.03
    - CUDA 版本：11.2
    
    下面是示例输出：

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:24:27 2021
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

1. 请将此会话保持打开状态，因为在学习本文的整个过程中，都要使用它来查看 Nvidia smi 输出。



## <a name="job-without-context-sharing"></a>未启用上下文共享时的作业

你将运行第一个作业在命名空间 `mynamesp1` 中的设备上部署应用程序。 此应用程序部署还将显示 GPU 上下文共享默认未启用。 

1. 列出命名空间中运行的所有 pod。 运行以下命令： 

    ```powershell
    kubectl get pods -n <Name of the namespace>
    ```

    下面是示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    No resources found.
    ```
1. 使用前面提供的 deployment.yaml 在设备上启动部署作业。 运行以下命令： 

    ```powershell
    kubectl apply -f <Path to the deployment .yaml> -n <Name of the namespace> 
    ```  

    此作业将创建两个容器，并在这两个容器中运行 n 体模拟。 模拟迭代次数在 `.yaml` 中指定。 有关详细信息，请参阅 [N 体模拟](https://physics.princeton.edu//~fpretori/Nbody/intro.htm)。
    
    下面是示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl apply -f -n mynamesp1 C:\gpu-sharing\k8-gpusharing.yaml
    job.batch/cuda-sample1 created
    job.batch/cuda-sample2 created
    PS C:\WINDOWS\system32>
    ```

1. 若要列出部署中已启动的 pod，请运行以下命令：

    ```powershell
    kubectl get pods -n <Name of the namespace>
    ```   

    下面是示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-27srm   1/1     Running   0          28s
    cuda-sample2-db9vx   1/1     Running   0          27s
    PS C:\WINDOWS\system32>
    ```

    设备上正在运行两个 pod：`cuda-sample1-cf979886d-xcwsq` 和 `cuda-sample2-68b4899948-vcv68`。

1. 提取 pod 的详细信息。 运行以下命令：

    ```powershell
    kubectl -n <Name of the namespace> describe <Name of the job> 
    ```  

    下面是示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 describe job.batch/cuda-sample1;  kubectl -n mynamesp1 describe job.batch/cuda-sample2
    Name:           cuda-sample1
    Namespace:      mynamesp1
    Selector:       controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
    Labels:         controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
                    job-name=cuda-sample1
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample1","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 12:25:34 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
               job-name=cuda-sample1
      Containers:
       cuda-sample-container1:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  60s   job-controller  Created pod: cuda-sample1-27srm
    Name:           cuda-sample2
    Namespace:      mynamesp1
    Selector:       controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
    Labels:         controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
                    job-name=cuda-sample2
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample2","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 12:25:35 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
               job-name=cuda-sample2
      Containers:
       cuda-sample-container2:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  60s   job-controller  Created pod: cuda-sample2-db9vx
    PS C:\WINDOWS\system32>
    ```
    此输出指示作业已成功创建这两个 pod。 

1. 当两个容器都在运行 n 体模拟时，请查看 Nvidia smi 输出中的 GPU 利用率。 转到设备的 PowerShell 界面并运行 `Get-HcsGpuNvidiaSmi`。

    下面是两个容器都在运行 n 体模拟时的示例输出：

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:26:41 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   64C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    197976      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    198051      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>    
    ```
    可以看到，GPU 0 上有两个容器（类型 = C）在 n 体模拟下运行。 

1. 监视 n 体模拟。 运行 `get pod` 命令。 下面是运行模拟时的示例输出。 

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-27srm   1/1     Running   0          70s
    cuda-sample2-db9vx   1/1     Running   0          69s
    PS C:\WINDOWS\system32>
    ```

    模拟完成后，输出中会指示已完成。 下面是示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS      RESTARTS   AGE
    cuda-sample1-27srm   0/1     Completed   0          2m54s
    cuda-sample2-db9vx   0/1     Completed   0          2m53s
    PS C:\WINDOWS\system32>
    ```
 
1. 完成模拟后，可以查看日志以及完成模拟所花费的总时间。 运行以下命令：

    ```powershell
    kubectl logs -n <Name of the namespace> <pod name>
    ``` 

    下面是示例输出： 

    ```powershell
    PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample1-27srm
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ===========// CUT //===================// CUT //=====================  
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170398.766 ms
    = 98.459 billion interactions per second
    = 1969.171 single-precision GFLOP/s at 20 flops per interaction
    PS C:\WINDOWS\system32>
    ```

    ```powershell
    PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample2-db9vx
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ===========// CUT //===================// CUT //=====================
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170368.859 ms
    = 98.476 billion interactions per second
    = 1969.517 single-precision GFLOP/s at 20 flops per interaction
    PS C:\WINDOWS\system32>    
    ```
1. 目前 GPU 上不应有任何进程正在运行。 可以使用 Nvidia smi 输出查看 GPU 利用率来验证这一点。

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:32:52 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   38C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
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

## <a name="job-with-context-sharing"></a>启用了上下文共享时的作业

你将运行第二个作业，以便在通过 MPS 启用了 GPU 上下文共享的情况下，在两个 CUDA 容器中部署 n 体模拟。 首先在设备上启用 MPS。

1. [连接到设备的 PowerShell 界面](azure-stack-edge-gpu-connect-powershell-interface.md)。

1. 若要在设备上启用 MPS，请运行 `Start-HcsGpuMPS` 命令。

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    
    Set compute mode to EXCLUSIVE_PROCESS for GPU 00002C74:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. 使用前面所用的同一部署 `yaml` 运行该作业。 可能需要删除现有的部署。 请参阅[删除部署](#delete-deployment)。

    下面是示例输出：

    ```yml
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 delete -f C:\gpu-sharing\k8-gpusharing.yaml
    job.batch "cuda-sample1" deleted
    job.batch "cuda-sample2" deleted
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    No resources found.
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 apply -f C:\gpu-sharing\k8-gpusharing.yaml
    job.batch/cuda-sample1 created
    job.batch/cuda-sample2 created
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-vcznt   1/1     Running   0          21s
    cuda-sample2-zkx4w   1/1     Running   0          21s
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 describe job.batch/cuda-sample1;  kubectl -n mynamesp1 describe job.batch/cuda-sample2
    Name:           cuda-sample1
    Namespace:      mynamesp1
    Selector:       controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
    Labels:         controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
                    job-name=cuda-sample1
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample1","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 21:51:51 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
               job-name=cuda-sample1
      Containers:
       cuda-sample-container1:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  46s   job-controller  Created pod: cuda-sample1-vcznt
    Name:           cuda-sample2
    Namespace:      mynamesp1
    Selector:       controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
    Labels:         controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
                    job-name=cuda-sample2
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample2","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 21:51:51 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
               job-name=cuda-sample2
      Containers:
       cuda-sample-container2:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  47s   job-controller  Created pod: cuda-sample2-zkx4w
    PS C:\WINDOWS\system32>
    ```

1. 当模拟正在运行时，可以查看 Nvidia smi 输出。 输出显示，与运行 n 体模拟的 CUDA 容器（M + C 类型）以及 MPS 服务（C 类型）对应的进程正在运行。 所有这些进程共享 GPU 0。

    ```powershell
    PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Mon Mar  3 21:54:50 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000E00B:00:00.0 Off |                    0 |
    | N/A   45C    P0    68W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    144377    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    144379    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    144443      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    ```

1. 完成模拟后，可以查看日志以及完成模拟所花费的总时间。 运行以下命令：

    ```powershell
        PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
        NAME                 READY   STATUS      RESTARTS   AGE
        cuda-sample1-vcznt   0/1     Completed   0          5m44s
        cuda-sample2-zkx4w   0/1     Completed   0          5m44s
        PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample1-vcznt
        Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
        ===========// CUT //===================// CUT //=====================    
        > Windowed mode
        > Simulation data stored in video memory
        > Single precision floating point simulation
        > 1 Devices used for simulation
        GPU Device 0: "Turing" with compute capability 7.5
        
        > Compute 7.5 CUDA device: [Tesla T4]
        40960 bodies, total time for 10000 iterations: 154979.453 ms
        = 108.254 billion interactions per second
        = 2165.089 single-precision GFLOP/s at 20 flops per interaction


        PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample2-zkx4w
        Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
        ===========// CUT //===================// CUT //=====================
        > Windowed mode
        > Simulation data stored in video memory
        > Single precision floating point simulation
        > 1 Devices used for simulation
        GPU Device 0: "Turing" with compute capability 7.5
        
        > Compute 7.5 CUDA device: [Tesla T4]
        40960 bodies, total time for 10000 iterations: 154986.734 ms
        = 108.249 billion interactions per second
        = 2164.987 single-precision GFLOP/s at 20 flops per interaction
        PS C:\WINDOWS\system32>
    ```
1. 模拟完成后，可以再次查看 Nvidia smi 输出。 只有 MPS 服务的 nvidia-cuda-mps-server 进程显示为正在运行。 

    ```powershell
    PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Mon Mar  3 21:59:55 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000E00B:00:00.0 Off |                    0 |
    | N/A   37C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    144443      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    ```

## <a name="delete-deployment"></a>删除部署

在设备上启用 MPS 以及禁用 MPS 的情况下运行时，可能需要删除部署。

若要在设备上删除部署，请运行以下命令： 

```powershell
kubectl delete -f <Path to the deployment .yaml> -n <Name of the namespace> 
```

下面是示例输出：

```powershell
PS C:\WINDOWS\system32> kubectl delete -f 'C:\gpu-sharing\k8-gpusharing.yaml' -n mynamesp1
deployment.apps "cuda-sample1" deleted
deployment.apps "cuda-sample2" deleted
PS C:\WINDOWS\system32>
```
    
## <a name="next-steps"></a>后续步骤

- [在 Azure Stack Edge Pro 上部署使用 GPU 共享的 IoT Edge 工作负载](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md)
