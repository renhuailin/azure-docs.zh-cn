---
title: 深度学习和人工智能框架
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine 上可用的深度学习框架和工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: michalmar
ms.author: mimarusa
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: a757151d17456c7ee9646bc0730a51f34088b255
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729182"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>适用于 Azure Data Science VM 的深度学习和人工智能框架
下面列出了 DSVM 上的深度学习框架。


## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA、cuDNN、NVIDIA 驱动程序](https://developer.nvidia.com/cuda-toolkit)

| 类别 | 值 |
|--|--|
| 支持的版本 | 11 |
| 支持的 DSVM 版本 | Windows Server 2019<br>Ubuntu 18.04 |
| 如何在 DSVM 上配置/安装它？ | _nvidia-smi_ 在系统路径上可用。 |
| 运行方式 | 打开命令提示符（在 Windows 上）或终端（在 Linux 上），然后运行 nvidia-smi。 |
## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| 类别 | 值 |
| ------------- | ------------- |
| 支持的版本 | 0.21.3|
| 支持的 DSVM 版本      | Ubuntu 18.04 |
| 如何在 DSVM 上配置/安装它？  | Horovod 安装在 Python 3.5 中 |
| 运行方式      | 在终端上激活正确的环境，然后运行 Python。 |


## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

| 类别 | 值 |
|--|--|
| 支持的版本 |  |
| 支持的 DSVM 版本 | Windows Server 2019<br>Ubuntu 18.04 |
| 用途 | 用于查询 GPU 活动的 NVIDIA 工具 |
| 如何在 DSVM 上配置/安装它？ | `nvidia-smi` 位于系统路径上。 |
| 运行方式 | 在具有 GPU 的虚拟机上，打开命令提示符（在 Windows 上）或终端（在 Linux 上），然后运行 `nvidia-smi`。 |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| 类别 | 值 |
|--|--|
| 支持的版本 | 1.9.0（Ubuntu 18.04、Windows 2019） |
| 支持的 DSVM 版本 | Windows Server 2019<br>Ubuntu 18.04 |
| 如何在 DSVM 上配置/安装它？ | 在 conda 环境 'py38_default', 'py38_pytorch' 下的 Python 中安装 |
| 运行方式 | 终端：激活正确的环境，然后运行 Python。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)：进行连接，然后打开 PyTorch 目录获取示例。 |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| 类别 | 值 |
|--|--|
| 支持的版本 | 2.5 |
| 支持的 DSVM 版本 | Windows Server 2019<br>Ubuntu 18.04 |
| 如何在 DSVM 上配置/安装它？ | 在 conda 环境 'py38_default', 'py38_tensorflow' 下的 Python 中安装 |
| 运行方式 | 终端：激活正确的环境，然后运行 Python。 <br/> * Jupyter：连接到 [Jupyter](provision-vm.md) 或 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然后打开 TensorFlow 目录获取示例。 |
