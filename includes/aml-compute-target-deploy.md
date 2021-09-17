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
ms.date: 05/20/2021
ms.openlocfilehash: e1e1ec9687890b43a6ce0c8f8bc361cfb91b61e2
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597814"
---
用于托管模型的计算目标将影响部署的终结点的成本和可用性。 使用此表选择合适的计算目标。

| 计算目标 | 用途 | GPU 支持 | FPGA 支持 | 说明 |
| ----- | ----- | ----- | ----- | ----- |
| [本地&nbsp;web&nbsp;服务](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | 测试/调试 | &nbsp; | &nbsp; | 用于有限的测试和故障排除。 硬件加速依赖于本地系统中库的使用情况。
| [Azure Kubernetes 服务 (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | 实时推理 <br/><br/> 建议用于生产工作负载。 |  [是](../articles/machine-learning/how-to-deploy-with-triton.md)（Web 服务部署） | [是](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |用于大规模生产部署。 提供所部署服务的快速响应时间和自动缩放。 不支持通过 Azure 机器学习 SDK 进行群集自动缩放。 若要更改 AKS 群集中的节点，请在 Azure 门户中使用 AKS 群集的 UI。 <br/><br/> 在设计器中受支持。 |
| [Azure 容器实例](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | 实时推理 <br/><br/> 建议仅用于开发/测试目的。| &nbsp;  | &nbsp; | 用于需要小于 48 GB RAM 的基于 CPU 的小规模工作负载。 不需要你管理群集。 <br/><br/> 在设计器中受支持。 |
| [Azure 机器学习计算群集](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | 批处理&nbsp;推理 | [是](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md)（机器学习管道） | &nbsp;  | 对无服务器计算运行批量评分。 支持普通 VM 和低优先级 VM。 不支持用于实时推理。|

> [!NOTE]
> 尽管计算目标（例如本地和 Azure 机器学习计算群集）支持使用 GPU 进行训练和试验，但在部署为 Web 服务时，只有 AKS 支持使用 GPU 进行推理。
>
> 当使用机器学习管道进行评分时，仅 Azure 机器学习计算支持使用 GPU 进行推理。
> 
> 选择群集 SKU 时，请先纵向扩展，然后横向扩展。从其 RAM 是模型所需量的 150% 的计算机开始，然后分析结果，找到具有所需性能的计算机。 了解这一信息后，增加计算机的数量，使其满足你的并发推理需求。

> [!NOTE]
> * 容器实例仅适用于小于 1 GB 的小模型。
> * 使用单节点 AKS 群集对大型模型进行开发/测试。