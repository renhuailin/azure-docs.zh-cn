---
title: include 文件
description: include 文件
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 11/02/2020
ms.openlocfilehash: 1f7abcdd1439fe5e6eeb2f718862f4875c61230c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509237"
---
用于托管模型的计算目标将影响部署的终结点的成本和可用性。 使用此表选择合适的计算目标。

| 计算目标 | 用途 | GPU 支持 | FPGA 支持 | 说明 |
| ----- | ----- | ----- | ----- | ----- |
| [本地&nbsp;web&nbsp;服务](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | 测试/调试 | &nbsp; | &nbsp; | 用于有限的测试和故障排除。 硬件加速依赖于本地系统中库的使用情况。
| [Azure Kubernetes 服务 (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | 实时推理 |  [是](../articles/machine-learning/how-to-deploy-inferencing-gpus.md)（Web 服务部署） | [是](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |用于大规模生产部署。 提供所部署服务的快速响应时间和自动缩放。 不支持通过 Azure 机器学习 SDK 进行群集自动缩放。 若要更改 AKS 群集中的节点，请在 Azure 门户中使用 AKS 群集的 UI。 <br/><br/> 在设计器中受支持。 |
| [Azure 容器实例](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | 测试或开发 | &nbsp;  | &nbsp; | 用于需要小于 48 GB RAM 的基于 CPU 的小规模工作负载。 <br/><br/> 在设计器中受支持。 |
| [Azure 机器学习计算群集](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | 批处理&nbsp;推理 | [是](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md)（机器学习管道） | &nbsp;  | 对无服务器计算运行批量评分。 支持普通 VM 和低优先级 VM。 |

> [!NOTE]
> 尽管计算目标（例如本地、Azure 机器学习计算和 Azure 机器学习计算群集）支持使用 GPU 进行定型和试验，但在部署为 Web 服务时，仅 AKS 支持使用 GPU 进行推理。
>
> 当使用机器学习管道进行评分时，仅 Azure 机器学习计算支持使用 GPU 进行推理。

> [!NOTE]
> * 容器实例仅适用于小于 1 GB 的小模型。
> * 使用单节点 AKS 群集对大型模型进行开发/测试。