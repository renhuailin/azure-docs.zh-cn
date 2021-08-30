---
title: 跨云区域的功能可用性
titleSuffix: Azure Machine Learning
description: 本文列出了公有云与 Azure 政府、Azure 德国和 Azure 中国世纪互联区域之间的功能可用性差异。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 07/16/2021
ms.custom: references_regions
ms.openlocfilehash: 1c26761fe23fb1a7558100dea3cb74d7b078550d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749498"
---
# <a name="azure-machine-learning-feature-availability-across-clouds-regions"></a>Azure 机器学习跨云区域的功能可用性

了解可在 Azure 政府、Azure 德国和 Azure 中国世纪互联区域使用的 Azure 机器学习功能。 

在全球 Azure 区域列表中，除了公有云区域之外，还有多个区域为特定市场提供服务。 例如，Azure 政府和 Azure 中国世纪互联区域。 除了公有云区域外，Azure 机器学习还部署到以下区域：

* Azure 政府区域 **美国-亚利桑那** 和 **美国-弗吉尼亚**。
* Azure 中国世纪互联区域 **中国东部 2**。

本文档其余部分的信息提供了有关这些区域中可用的 Azure 机器学习功能的信息，以及有关使用这些功能的区域特定信息。
## <a name="azure-government"></a>Azure Government 

| 功能 | 公有云状态  | 美国-弗吉尼亚 | 美国-亚利桑那| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **[自动化机器学习](concept-automated-ml.md)** | | | |
| 在笔记本中创建和运行试验                                    | GA                   | YES                | YES         |
| 在工作室 Web 体验中创建和运行试验                        | 公共预览版       | YES                | YES         |
| 行业领先的预测功能                                  | GA                   | YES                | YES         |
| 支持深度学习和其他高级学习器                      | GA                   | YES                | YES         |
| 大数据支持（最多 100 GB）                                          | 公共预览版       | YES                | YES         |
| Azure Databricks 集成                                              | GA                   | 是                 | 是          |
| SQL、CosmosDB 和 HDInsight 集成                                   | GA                   | YES                | YES         |
| **[机器学习管道](concept-ml-pipelines.md)** |   |  | | 
| 使用 Azure ML SDK 创建、运行和发布管道                   | GA                   | YES                | YES         |
| 使用 Azure ML SDK 创建管道终结点                           | GA                   | YES                | YES         |
| 使用 Azure ML SDK 创建、编辑和删除计划的管道运行 | GA                   | 是*               | 是*        |
| 在工作室中查看管道运行详细信息                                        | GA                   | YES                | YES         |
| 在 Azure ML 设计器中创建、运行、可视化和发布管道          | GA      | YES                | YES         |
| Azure Databricks 与 ML 管道的集成                             | GA                   | 是                 | 是          |
| 在 Azure ML 设计器中创建管道终结点                             | GA      | YES                | YES         |
| **[集成式笔记本](how-to-run-jupyter-notebooks.md)** |   |  | | 
| 工作区笔记本和文件共享                                        | GA                   | YES                | YES         |
| R 和 Python 支持                                                       | GA                   | YES                | YES         |
| 虚拟网络支持                                                    | 公共预览版       | 是                 | 是          |
| **[计算实例](concept-compute-instance.md)** |   |  | | 
| 集成式笔记本的托管计算实例                         | GA                   | YES                | YES         |
| Jupyter、JupyterLab 集成                                            | GA                   | YES                | YES         |
| 虚拟网络 (VNet) 支持                                             | 公共预览版       | YES                | YES         |
| **SDK 支持** |  |  | | 
| [Python SDK 支持](/python/api/overview/azure/ml/)                                                         | GA                   | YES                | YES         |
| **[安全性](concept-enterprise-security.md)** |   | | | 
| 虚拟网络 (VNet) 训练支持                                | GA                   | YES                | YES         |
| 虚拟网络 (VNet) 推理支持                               | GA                   | YES                | YES         |
| 对终结点身份验证评分                                            | 公共预览版       | YES                | YES         |
| 工作区专用终结点                                                 | GA  |  公共预览版  |  公共预览版 |
| VNet 后的 ACI                                                            | 公共预览版       | 是                 | 是          |
| VNet 后的 ACR                                                            | GA       | YES                 | YES          |
| AKS 群集的专用 IP                                                  | 公共预览版       | 是                 | 是          |
| **计算** |   | | |
| [跨工作区配额管理](how-to-manage-quotas.md)                                         | GA                   | YES                | YES         |
| **[用于机器学习的数据](concept-data.md)** |   | | |
| 通过 SDK 创建、查看或编辑数据集和数据存储                  | GA                   | YES                | YES         |
| 通过 UI 创建、查看或编辑数据集和数据存储                   | GA                   | YES                | YES         |
| 通过 SDK 查看、编辑或删除数据集偏移监视器                   | 公共预览版       | YES                | YES         |
| 通过 UI 查看、编辑或删除数据集偏移监视器                    | 公共预览版       | YES                | YES         |
| **机器学习生命周期** |   | | |
| [模型分析](how-to-deploy-profile-model.md)                                                            | GA                   | YES                | PARTIAL     |
| [Azure ML CLI 1.0](reference-azure-machine-learning-cli.md)     | GA                   | YES                | YES         |
| [基于 FPGA 的硬件加速模型](how-to-deploy-fpga-web-service.md)                                     | GA                   | 是                 | 是          |
| [Visual Studio Code 集成](how-to-setup-vs-code.md)                                             | 公共预览版       | 是                 | 是          |
| [事件网格集成](how-to-use-event-grid.md)                                                     | 公共预览版       | 是                 | 是          |
| [将 Azure 流分析与 Azure 机器学习集成](../stream-analytics/machine-learning-udf.md)               | 公共预览版       | 是                 | 是          |
| **[标记](how-to-create-labeling-projects.md)** |   | | |
| 标记项目管理门户                                        | GA                   | YES                | YES         |
| 标记工具门户                                                            | GA                   | YES                | YES         |
| 使用私人劳动力进行标记                                          | GA                   | YES                | YES         |
| ML 辅助标记（图像分类和对象检测）           | 公共预览版       | YES                | YES         |
| **[负责的 ML](concept-responsible-ml.md)** |   | | |
| UI 的可说明性                                                       | 公共预览版       | 是                 | 是          |
| 差异隐私 SmartNoise 工具包                                    | OSS                  | 是                 | 是          |
| Azure 机器学习中用于实现数据表的自定义标记              | GA                   | 是                 | 是          |
| 公平 AzureML 集成                                               | 公共预览版       | 是                 | 是          |
| 可解释性 SDK                                                      | GA                   | YES                | YES         |
| **培训** |   | | |
| [试验日志流式处理](how-to-track-monitor-analyze-runs.md)                                              | GA                   | YES                | YES         |
| [强化学习](how-to-use-reinforcement-learning.md)                                                     | 公共预览版       | 是                 | 是          |
| [试验 UI](how-to-track-monitor-analyze-runs.md)                                                         | 公共预览版                   | YES                | YES         |
| [.NET 集成 ML.NET 1.0](/dotnet/machine-learning/tutorials/object-detection-model-builder)                                                | GA                   | YES                | YES         |
| **推理** |   | | |
| [批处理推理](tutorial-pipeline-batch-scoring-classification.md)                                                          | GA                   | YES                | YES         |
| [带 FPGA 的 Data Box Edge](how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)                                                    | 公共预览版       | 是                 | 是          |
| **其他** |   | | |
| [开放数据集](../open-datasets/samples.md)                                                              | 公共预览版       | YES                | YES         |
| [自定义认知搜索](how-to-deploy-model-cognitive-search.md)                                                    | 公共预览版       | YES                | YES         |


### <a name="azure-government-scenarios"></a>Azure 政府方案

| 方案                                                    | 美国-弗吉尼亚 | 美国-亚利桑那| 限制  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **常规安全设置** |   | | |
| 禁用/控制 Internet 访问（入站和出站）和特定 VNet | PARTIAL| PARTIAL   |  | 
| 所有关联资源/服务的放置  | YES | YES |  |
| 静态加密和传输中加密。                                                 | YES | YES |  |
| 对计算资源的 Root 和 SSH 访问。                                          | YES | YES |  |
| 维护已部署系统（实例、终结点等）的安全性，包括终结点保护、修补和日志记录 |  PARTIAL|  PARTIAL |VNet 后面的 ACI 目前不可用 |                                  
| 控制（禁用/限制/约束）ACI/AKS 集成的使用                    | PARTIAL| PARTIAL |VNet 后面的 ACI 目前不可用|
| Azure 基于角色的访问控制 (Azure RBAC) - 自定义角色创建                           | YES | YES |  |
| 控制对 ML 服务使用的 ACR 映像的访问（Azure 提供/维护的与自定义的）  |PARTIAL|  PARTIAL |  |
| **机器学习服务常规使用情况** |  | | |
| 能够使用开发环境构建模型、训练该模型、将其作为终结点托管并通过 WebApp 使用它     | YES | YES |  |
| 能够从 ADLS (Data Lake Storage) 中拉取数据                                 |YES | YES |  |
| 能够从 Azure Blob 存储中拉取数据                                       |YES | YES |  |



### <a name="other-azure-government-limitations"></a>Azure 政府的其他限制

* 对于 Azure 机器学习计算实例，Azure 政府版中未提供对持续时间超过 24 小时的令牌进行刷新的功能。
* 模型分析在美国-亚利桑那区域不支持 4 CPU。   
* 示例笔记本在需要访问公共数据的情况下可能无法在 Azure 政府版中使用。
* IP 地址：[所需的公共 Internet 访问](how-to-secure-training-vnet.md#required-public-internet-access)说明中使用的 CLI 命令不会返回 IP 范围。 请改用[适用于 Azure 政府版的 Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=57063)。
* 对于计划的管道，我们还提供了基于 blob 的触发器机制。 CMK 工作区不支持此机制。 若要为 CMK 工作区启用基于 blob 的触发器，必须进行其他设置。 有关详细信息，请参阅[从逻辑应用触发机器学习管道的运行](how-to-trigger-published-pipeline.md)。
* 防火墙：使用 Azure 政府区域时，请将下述主机添加到防火墙设置：

    * 对于亚利桑那，请使用 `usgovarizona.api.ml.azure.us`
    * 对于弗吉尼亚，请使用 `usgovvirginia.api.ml.azure.us`
    * 对于两者，请使用 `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure 中国世纪互联 

| 功能                                       | 公有云状态 | CH-East-2 | CH-North-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **自动化机器学习** |    | | |
| 在笔记本中创建和运行试验                                    | GA               | YES       | 空值        |
| 在工作室 Web 体验中创建和运行试验                        | 公共预览版   | YES       | 空值        |
| 行业领先的预测功能                                  | GA               | YES       | 空值        |
| 支持深度学习和其他高级学习器                      | GA               | YES       | 空值        |
| 大数据支持（最多 100 GB）                                          | 公共预览版   | YES       | 空值        |
| Azure Databricks 集成                                              | GA               | 是        | 空值        |
| SQL、CosmosDB 和 HDInsight 集成                                   | GA               | YES       | 空值        |
| **机器学习管道** |    | | |
| 使用 Azure ML SDK 创建、运行和发布管道                   | GA               | YES       | 空值        |
| 使用 Azure ML SDK 创建管道终结点                           | GA               | YES       | 空值        |
| 使用 Azure ML SDK 创建、编辑和删除计划的管道运行 | GA               | YES       | 空值        |
| 在工作室中查看管道运行详细信息                                        | GA               | YES       | 空值        |
| 在 Azure ML 设计器中创建、运行、可视化和发布管道          | GA  | YES       | 空值        |
| Azure Databricks 与 ML 管道的集成                             | GA               | 是        | 空值        |
| 在 Azure ML 设计器中创建管道终结点                             | GA   | YES       | 空值        |
| **集成式笔记本** |   | | |
| 工作区笔记本和文件共享                                        | GA               | YES       | 空值        |
| R 和 Python 支持                                                       | GA               | YES       | 空值        |
| 虚拟网络支持                                                    | 公共预览版   | 是        | 空值        |
| **计算实例** |    | | |
| 集成式笔记本的托管计算实例                         | GA               | YES        | 空值        |
| Jupyter、JupyterLab 集成                                            | GA               | YES       | 空值        |
| 虚拟网络 (VNet) 支持                                             | 公共预览版   | YES       | 空值        |
| **SDK 支持** |    | | |
| Python SDK 支持                                                         | GA               | YES       | 空值        |
| **安全性** |   | | |
| 虚拟网络 (VNet) 训练支持                                | GA               | YES       | 空值        |
| 虚拟网络 (VNet) 推理支持                               | GA               | YES       | 空值        |
| 对终结点身份验证评分                                            | 公共预览版   | YES       | 空值        |
| 工作区专用终结点                                                 | GA               | 是        | 空值        |
| VNet 后的 ACI                                                            | 公共预览版   | 是        | 空值        |
| VNet 后的 ACR                                                            | GA   | YES       | 空值        |
| AKS 群集的专用 IP                                                  | 公共预览版   | 是        | 空值        |
| **计算** |   | | |
| 跨工作区配额管理                                         | GA               | YES       | 空值        |
| **用于机器学习的数据** | | | |
| 通过 SDK 创建、查看或编辑数据集和数据存储                  | GA               | YES       | 空值        |
| 通过 UI 创建、查看或编辑数据集和数据存储                   | GA               | YES       | 空值        |
| 通过 SDK 查看、编辑或删除数据集偏移监视器                   | 公共预览版   | YES       | 空值        |
| 通过 UI 查看、编辑或删除数据集偏移监视器                    | 公共预览版   | YES       | 空值        |
| **机器学习生命周期** |    | | |
| 模型分析                                                            | GA               | PARTIAL   | 空值        |
| 适用于机器学习和 Azure ML CLI 的 Azure DevOps 扩展         | GA               | YES       | 空值        |
| 基于 FPGA 的硬件加速模型                                     | GA               | 是        | 空值        |
| Visual Studio Code 集成                                             | 公共预览版   | 是        | 空值        |
| 事件网格集成                                                     | 公共预览版   | YES       | 空值        |
| 将 Azure 流分析与 Azure 机器学习集成               | 公共预览版   | 是        | 空值        |
| **标记** |    | | |
| 标记项目管理门户                                        | GA               | YES       | 空值        |
| 标记工具门户                                                            | GA               | YES       | 空值        |
| 使用私人劳动力进行标记                                          | GA               | YES       | 空值        |
| ML 辅助标记（图像分类和对象检测）           | 公共预览版   | YES       | 空值        |
| **负责的 ML** |    | | |
| UI 的可说明性                                                       | 公共预览版   | 是        | 空值        |
| 差异隐私 SmartNoise 工具包                                    | OSS              | 是        | 空值        |
| Azure 机器学习中用于实现数据表的自定义标记              | GA               | 是        | 空值        |
| 公平 AzureML 集成                                               | 公共预览版   | 是        | 空值        |
| 可解释性 SDK                                                      | GA               | YES       | 空值        |
| **培训** |    | | |
| 试验日志流式处理                                              | GA               | YES       | 空值        |
| 强化学习                                                     | 公共预览版   | 是        | 空值        |
| 试验 UI                                                         | GA               | YES       | 空值        |
| .NET 集成 ML.NET 1.0                                                | GA               | YES       | 空值        |
| **推理** |   | | |
| 批处理推理                                                          | GA               | YES       | 空值        |
| 带 FPGA 的 Data Box Edge                                                    | 公共预览版   | 是        | 空值        |
| **其他** |    | | |
| 开放数据集                                                              | 公共预览版   | YES       | 空值        |
| 自定义认知搜索                                                    | 公共预览版   | YES       | 空值        |



### <a name="other-azure-china-limitations"></a>Azure 中国的其他限制

* Azure 中国具有有限的 VM SKU（特别是对于 GPU SKU 而言）。 它仅包含 NCv3 系列 (V100)。
* REST API 终结点不同于全局 Azure。 请使用下表查找 Azure 中国区域的 REST API 终结点：

    | REST 终结点                 | 全球 Azure                                 | 中国-政府                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | 管理平面 | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | 数据平面       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* 示例笔记本在需要访问公共数据的情况下可能无法使用。
* IP 地址范围：[所需的公共 Internet 访问](how-to-secure-training-vnet.md#required-public-internet-access)说明中使用的 CLI 命令不会返回 IP 范围。 请改用[适用于 Azure 中国版的 Azure IP 范围和服务标记](https://www.microsoft.com//download/details.aspx?id=57062)。
* 目前，启用了专用终结点的工作区不支持 Azure 机器学习计算实例预览版，但在下一次将服务扩展部署到所有 AML 区域时，会支持 CI。

## <a name="next-steps"></a>后续步骤

若要详细了解提供 Azure 机器学习的区域，请参阅[各区域的产品](https://azure.microsoft.com/global-infrastructure/services/)。