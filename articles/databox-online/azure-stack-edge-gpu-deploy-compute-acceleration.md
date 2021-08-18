---
title: 在 Azure Stack Edge 设备上使用计算加速 GPU 或 VPU 进行 Kubernetes 部署 | Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 或 Azure Stack Edge Mini Ri 上使用计算加速 GPU 或 VPU 进行 Kubernetes 部署。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/26/2021
ms.author: alkohli
ms.openlocfilehash: ad071118eabafdfeaddcf1e2a4738c646f62986d
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114719989"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>在 Azure Stack Edge Pro GPU 上使用计算加速进行 Kubernetes 部署

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何在使用 Kubernetes 部署时在 Azure Stack Edge 设备上使用计算加速。 


## <a name="about-compute-acceleration"></a>关于计算加速 

中央处理器 (CPU) 是计算机上运行的大多数进程的默认常规用途计算单元。 通常，使用专用计算机硬件可以比在 CPU 上的软件中执行更多功能且更高效。 例如，图形处理单元 (GPU) 可用于加速像素数据的处理。  

计算加速是专门用于 Azure Stack Edge 设备的术语，其中使用图形处理单元 (GPU)、视觉处理单元 (VPU) 或现场可编程门阵列 (FPGA) 来实现硬件加速。 在 Azure Stack Edge 设备上部署的大多数工作负载都涉及到关键计时、多个相机流和/或高帧速率，这些都需要特定的硬件加速。

本文将讨论只使用 GPU 或 VPU 为以下设备进行计算加速：

- **Azure Stack Edge Pro GPU** - 这些设备可以有 1 个或 2 个 Nvidia T4 Tensor Core GPU。 有关详细信息，请参阅 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)。
- **Azure Stack Edge Pro R** - 这些设备有 1 个 Nvidia T4 Tensor Core GPU。 有关详细信息，请参阅 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)。
- **Azure Stack Edge Mini R** - 这些设备有 1 个 Intel Movidius Myriad X VPU。 有关详细信息，请参阅 [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX)。


## <a name="use-gpu-for-kubernetes-deployment"></a>使用 GPU 进行 Kubernetes 部署

以下示例 `yaml` 可用于 Azure Stack Edge Pro GPU 或具有 GPU 的 Azure Stack Edge Pro R 设备。

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>使用 VPU 进行 Kubernetes 部署

以下示例 `yaml` 可用于具有 VPU 的 Azure Stack Edge Mini R 设备。

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>后续步骤

了解如何[使用 kubectl 在 Azure Stack Edge Pro GPU 设备上运行具有 PersistentVolume 的 Kubernetes 有状态应用程序](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)。
