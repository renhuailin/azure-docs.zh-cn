---
title: 在用于 Kubernetes 部署 Azure Stack Edge 设备上使用计算加速 GPU 或 VPU |Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro GPU 上使用计算加速 GPU 或 VPU，Azure Stack Edge Pro R 或 Azure Stack 边缘微型 Ri 进行 Kubernetes 部署。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/05/2020
ms.author: alkohli
ms.openlocfilehash: 0aaad18ba5bf98ca2ad53bd86605dfc6cce3e52c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466073"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>在 Kubernetes 部署 Azure Stack Edge Pro GPU 上使用计算加速

本文介绍如何在使用 Kubernetes 部署时在 Azure Stack Edge 设备上使用计算加速。 本文适用于 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack 边缘迷你 R 设备。


## <a name="about-compute-acceleration"></a>关于计算加速 

中央处理器 (CPU) 是计算机上运行的大多数进程的默认常规用途计算。 通常，专用的计算机硬件用于更有效地执行某些功能，而不是在 CPU 中运行软件。 例如， (GPU) 的图形处理单元可用于加速像素数据的处理。  

计算加速是专门用于 Azure Stack 边缘设备的术语，其中的图形处理单元 (GPU) 、视觉处理单元 (VPU) 或现场可编程入口阵列 (FPGA) 用于硬件加速。 部署在 Azure Stack 边缘设备上的大多数工作负载都涉及到关键计时、多个相机流和/或高帧速率，所有这些工作负荷都需要特定硬件加速。

本文将仅使用 GPU 或 VPU 为以下设备讨论计算加速：

- **Azure Stack Edge PRO GPU** -这些设备可以具有1个或2个 Nvidia T4 TENSOR Core GPU。 有关详细信息，请参阅 [NVIDIA T4](https://www.nvidia.com/data-center/tesla-t4/)。
- **Azure Stack Edge Pro R** -这些设备具有1个 Nvidia T4 TENSOR Core GPU。 有关详细信息，请参阅 [NVIDIA T4](https://www.nvidia.com/data-center/tesla-t4/)。
- **Azure Stack 边缘迷你 R** -这些设备具有1个 Intel Movidius 无数 X VPU。 有关详细信息，请参阅 [Intel Movidius 无数 X VPU](https://www.movidius.com/MyriadX)。


## <a name="use-gpu-for-kubernetes-deployment"></a>使用 GPU 进行 Kubernetes 部署

以下示例 `yaml` 可用于 Azure Stack Edge PRO GPU 或带有 GPU 的 Azure Stack Edge Pro R 设备。

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

以下示例 `yaml` 可用于具有 VPU 的 Azure Stack Edge 迷你 R 设备。

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

了解如何 [使用 kubectl 在 Azure Stack Edge PRO GPU 设备上使用 PersistentVolume 运行 Kubernetes 有状态应用程序](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)。
