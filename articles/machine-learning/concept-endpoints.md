---
title: 什么是终结点（预览版）？
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习终结点（预览版）如何简化机器学习部署。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2
ms.date: 06/17/2021
ms.openlocfilehash: 4a4cc34b3f3bb77e0c2405d3b0a29b40fa1cd616
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426994"
---
# <a name="what-are-azure-machine-learning-endpoints-preview"></a>什么是 Azure 机器学习终结点（预览版）？ 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

使用 Azure 机器学习终结点（预览版）可以简化实时部署和批量推理部署的模型部署。 终结点提供了一个统一的接口，以用于跨计算类型调用和管理模型部署。

本文介绍：
> [!div class="checklist"]
> * 终结点
> * 部署
> * 托管联机终结点
> * Azure Kubernetes 服务 (AKS) 联机终结点
> * 批量推理终结点

## <a name="what-are-endpoints-and-deployments-preview"></a>什么是终结点和部署（预览版）？

训练机器学习模型后，需要部署该模型，以便其他人可以使用它来执行推理。 在 Azure 机器学习中，可以使用终结点（预览版）和部署（预览版）来执行此操作 。

:::image type="content" source="media/concept-endpoints/endpoint-concept.png" alt-text="显示终结点如何将流量拆分到两个部署的示意图":::

终结点是一个 HTTPS 终结点，客户端可以调用它来接收已训练模型的推理（评分）输出。 提供以下功能： 
- 使用基于“密钥和令牌”的身份验证进行身份验证 
- SSL 终止 
- 部署之间的流量分配 
- 稳定的评分 URI (endpoint-name.region.inference.ml.azure.com)


部署是一组计算资源，承载执行实际推理的模型。 该结构包含： 
- 模型详细信息（代码、模型、环境） 
- 计算资源和缩放设置 
- 高级设置（例如请求和探测设置）

一个终结点可以包含多个部署。 终结点和部署是显示在 Azure 门户中的独立 ARM 资源。

Azure 机器学习使用终结点和部署的概念实现不同类型的终结点：[联机终结点](#what-are-online-endpoints-preview)和[批处理终结点](#what-are-batch-endpoints-preview) 。

### <a name="multiple-developer-interfaces"></a>多个开发人员界面

使用多个开发人员工具来创建和管理批处理终结点与联机终结点：
- Azure CLI
- ARM/REST API
- Azure 机器学习工作室 Web 门户
- Azure 门户（IT/管理员）
- 使用 Azure CLI 接口和 REST/ARM 接口支持 CI/CD MLOps 管道

## <a name="what-are-online-endpoints-preview"></a>什么是联机终结点（预览版）？

联机终结点（预览版）是用于联机（实时）推理的终结点。 与批处理终结点相比，联机终结点包含已准备好从客户端接收数据并可实时发回响应的部署  。

### <a name="online-endpoints-requirements"></a>联机终结点要求

若要创建联机终结点，需要指定以下各项：
- 模型文件（或指定工作区中已注册的模型） 
- 评分脚本 - 执行评分/推理所需的代码
- 环境 - 包含 Conda 依赖项的 Docker 映像，或 Dockerfile 
- 计算实例和缩放设置 

了解如何从 [CLI](how-to-deploy-managed-online-endpoints.md) 和[工作室 Web 门户](how-to-use-managed-online-endpoint-studio.md)部署联机终结点。

### <a name="test-and-deploy-locally-for-faster-debugging"></a>在本地测试和部署以加快调试速度

在本地部署以测试终结点，而无需部署到云。 Azure 机器学习创建一个模拟 Azure ML 映像的本地 Docker 映像。 Azure 机器学习将在本地生成并运行部署，并缓存映像以快速迭代。

### <a name="native-bluegreen-deployment"></a>本机蓝/绿部署 

如前所述，一个终结点可以包含多个部署。 联机终结点可以执行负载均衡，以将任何百分比的流量分配给每个部署。

使用流量分配可以通过在不同实例之间均衡请求来执行安全推出蓝/绿部署。

:::image type="content" source="media/concept-endpoints/traffic-allocation.png" alt-text="显示用于设置部署之间流量分配的滑块界面的屏幕截图":::

了解如何[安全推出到联机终结点](how-to-safely-rollout-managed-endpoints.md)。

### <a name="application-insights-integration"></a>Application Insights 集成

所有联机终结点均与 Application Insights 集成，以监视 SLA 并诊断问题。 

但是，[托管联机终结点](#managed-online-endpoints-vs-aks-online-endpoints-preview)还包括与 Azure 日志和 Azure 指标的现成集成。

### <a name="security"></a>安全性

- 身份验证：密钥和 Azure ML 令牌
- 托管标识：用户分配和系统分配（仅限托管联机终结点）
- 终结点调用默认使用 SSL


## <a name="managed-online-endpoints-vs-aks-online-endpoints-preview"></a>托管联机终结点与 AKS 联机终结点（预览版）的比较

有两种类型的联机终结点：托管联机终结点（预览版）和 AKS 联机终结点（预览版） 。 下表突出显示了两者的一些主要差异。

|  | 托管联机终结点 | AKS 联机终结点 |
|-|-|-|
| 建议的用户 | 需要托管模型部署和增强式 MLOps 体验的用户 | 偏好 Azure Kubernetes 服务 (AKS) 并可自行管理基础结构要求的用户 |
| **基础结构管理** | 托管计算预配、缩放、主机 OS 映像更新和安全强化 | 用户责任 |
| **计算类型** | 托管 (AmlCompute) | AKS |
| 现成的监视功能 | [Azure 监视](how-to-monitor-online-endpoints.md) <br> （包括延迟和吞吐量等关键指标） | 不支持 |
| 现成的日志记录 | [终结点级别的 Azure 日志和 Log Analytics](how-to-deploy-managed-online-endpoints.md#optional-integrate-with-log-analytics) | 在群集级别手动设置 |
| **Application Insights** | 支持 | 支持 |
| **托管的标识** | [支持](tutorial-deploy-managed-endpoints-using-system-managed-identity.md) | 不支持 |
| 虚拟网络 (VNET) | 不支持（公共预览版） | 在群集级别手动配置 |
| 查看成本 | [终结点和部署级别](how-to-view-online-endpoints-costs.md) | 群集级别 |

### <a name="managed-online-endpoints"></a>托管联机终结点

托管联机终结点有助于简化部署过程。 与 AKS 联机终结点相比，托管联机终结点提供以下优势：

- 托管基础结构
    - 自动预配计算并托管模型（你只需指定 VM 类型和缩放设置） 
    - 自动对基础主机 OS 映像执行更新和修补
    - 发生系统故障时自动恢复节点

:::image type="content" source="media/concept-endpoints/log-analytics-and-azure-monitor.png" alt-text="显示 Azure Monitor 终结点延迟图的屏幕截图":::

- 监视和日志
    - 使用[与 Azure Monitor 的原生集成](how-to-monitor-online-endpoints.md)来监视模型可用性、性能和 SLA。
    - 使用日志以及与 Azure Log Analytics 的原生集成来调试部署。

- 托管标识
    -  使用[托管标识从评分脚本访问受保护资源](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)

:::image type="content" source="media/concept-endpoints/endpoint-deployment-costs.png" alt-text="终结点和部署的成本图的屏幕截图":::

- 查看成本 
    - 通过托管联机终结点可[在终结点和部署级别监视成本](how-to-view-online-endpoints-costs.md)

有关分步教程，请参阅[如何部署托管联机终结点](how-to-deploy-managed-online-endpoints.md)。

## <a name="what-are-batch-endpoints-preview"></a>什么是批处理终结点（预览版）？

批处理终结点（预览版）是用于针对一段时间内的大量数据执行批量推理的终结点。  批处理终结点接收指向数据的指针并以异步方式运行作业，以在计算群集上并行处理数据。 批处理终结点将输出存储到数据存储供进一步分析。

了解如何[通过 Azure CLI 部署和使用批处理终结点](how-to-use-batch-endpoint.md)。

### <a name="no-code-mlflow-model-deployments"></a>无代码 MLflow 模型部署

使用 [MLflow 模型](how-to-use-mlflow.md)的无代码批处理终结点创建体验来自动创建评分脚本和执行环境。  

对于使用 MLflow 模型的批处理终结点，需要指定以下各项：
- 模型文件（或指定工作区中已注册的模型）
- 计算目标

但是，如果你部署的不是 MLflow 模型，则需要提供其他所需信息：
- 评分脚本 - 执行评分/推理所需的代码
- 环境 - 包含 Conda 依赖项的 Docker 映像


### <a name="managed-cost-with-autoscaling-compute"></a>使用自动缩放计算的托管成本

调用批处理终结点会触发异步批量推理作业。 作业启动时将自动预配计算资源，作业完成时将自动取消分配计算资源。 因此，你只需在使用计算时付费。

对于每个批量推理作业，可以[替代计算资源设置](how-to-use-batch-endpoint.md#overwrite-settings)（例如实例计数）和高级设置（例如最小批大小、错误阈值等），以加快执行速度并降低成本。

### <a name="flexible-data-sources-and-storage"></a>灵活的数据源和存储

调用批处理终结点时，可为输入数据使用以下选项：

- Azure 机器学习注册的数据集 - 有关详细信息，请参阅[创建 Azure 机器学习数据集](how-to-train-with-datasets.md)
- 云数据 - 公共数据 URI 或数据存储中的数据路径。 有关详细信息，请参阅[使用 Azure 机器学习工作室连接到数据](how-to-connect-data-ui.md)
- 本地存储的数据

将存储输出位置指定为任何数据存储和路径。 默认情况下，批处理终结点将其输出存储在工作区的默认 Blob 存储中，该存储按作业名称（系统生成的 GUID）进行组织。

### <a name="security"></a>安全性

- 身份验证：Azure Active Directory 令牌
- 终结点调用默认使用 SSL

## <a name="next-steps"></a>后续步骤

- [如何使用 Azure CLI 部署托管联机终结点](how-to-deploy-managed-online-endpoints.md)
- [如何使用 Azure CLI 部署批处理终结点](how-to-use-batch-endpoint.md)
- [如何通过工作室使用托管联机终结点](how-to-use-managed-online-endpoint-studio.md)
- [通过 REST 部署模型（预览版）](how-to-deploy-with-rest.md)
- [如何监视托管联机终结点](how-to-monitor-online-endpoints.md)
- [如何查看联机终结点成本](how-to-view-online-endpoints-costs.md)
- [管理和增大 Azure 机器学习资源的配额](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
