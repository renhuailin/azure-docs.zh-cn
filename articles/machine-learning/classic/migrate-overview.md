---
title: ML 工作室（经典）：迁移到 Azure 机器学习
description: 从工作室（经典）迁移到现代化数据科学平台的 Azure 机器学习。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 52f682be784db8f3bb2f472caa60023f66c10e48
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580523"
---
# <a name="migrate-to-azure-machine-learning"></a>迁移到 Azure 机器学习 

本文介绍了如何将机器学习工作室（经典）资产迁移到 [Azure 机器学习](../index.yml)。 此时，必须手动重新生成试验才能迁移资源。

Azure 机器学习提供了一个现代化的数据科学平台，该平台将无代码和代码优先方法相结合。 若要详细了解工作室（经典）和 Azure 机器学习之间的差异，请参阅[评估 Azure 机器学习](#step-1-assess-azure-machine-learning)部分。


## <a name="recommended-approach"></a>推荐的方法

若要迁移到 Azure 机器学习，我们建议采用以下方法：

> [!div class="checklist"]
> * 步骤 1：评估 Azure 机器学习
> * 步骤 2：创建迁移计划
> * 步骤 3：重新生成试验和 Web 服务
> * 步骤 4：集成客户端应用
> * 步骤 5：清理 ML 工作室（经典）资产


## <a name="step-1-assess-azure-machine-learning"></a>步骤 1：评估 Azure 机器学习
1. 了解 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)；它的优点、成本和体系结构。

1. 将 Azure 机器学习和工作室（经典）的功能进行比较。

    >[!NOTE]
    > Azure 机器学习中的设计器功能提供与工作室（经典）类似的拖放体验。 但是，Azure 机器学习还提供了可靠的[代码优先工作流](../concept-model-management-and-deployment.md)作为替代方法。 此迁移系列重点介绍了设计器，因为它与工作室（经典）体验最为相似。

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. 验证你的关键工作室（经典）模块在 Azure 机器学习设计器中是否受支持。 有关详细信息，请参阅下面的[工作室（经典）和设计器模块映射](#studio-classic-and-designer-module-mapping)表。

4. [创建 Azure 机器学习工作区](../how-to-manage-workspace.md?tabs=azure-portal)。

## <a name="step-2-create-a-migration-plan"></a>步骤 2：创建迁移计划

1. 确定要迁移的工作室（经典）数据集、模块和 Web 服务。

1. 确定迁移将对你的业务造成的影响。

1. 创建迁移计划。

## <a name="step-3-rebuild-experiments-and-web-services"></a>步骤 3：重新生成试验和 Web 服务

1. [将数据集迁移到 Azure 机器学习](migrate-register-dataset.md)。
1. 使用设计器[重新生成试验](migrate-rebuild-experiment.md)。
1. 使用设计器[重新部署 Web 服务](migrate-rebuild-web-service.md)。

    >[!NOTE]
    > Azure 机器学习还支持数[据集](../how-to-create-register-datasets.md)、[训练](../how-to-set-up-training-targets.md)[和部署](../how-to-deploy-and-where.md)的代码优先工作流。

## <a name="step-4-integrate-client-apps"></a>步骤 4：集成客户端应用

1. 修改调用工作室（经典）Web 服务的客户端应用程序，以使用新的 [Azure 机器学习终结点](migrate-rebuild-integrate-with-client-app.md)。

## <a name="step-5-cleanup-studio-classic-assets"></a>步骤 5：清理工作室（经典）资产

1. [清理工作室（经典）资产](export-delete-personal-data-dsr.md)，避免产生额外费用。 在验证 Azure 机器学习工作负载之前，你可能想要保留资产以进行回退。

## <a name="studio-classic-and-designer-module-mapping"></a>工作室（经典）和设计器模块映射

请查阅下表，了解在设计器中重建工作室（经典）试验时要使用的模块。


> [!IMPORTANT]
> 设计器通过开源 Python 包而不是工作室（经典）等 C# 包实现模块。 由于存在这种差异，设计器模块的输出可能会略微不同于其工作室（经典）对应项。


|类别|工作室（经典）模块|替换设计器模块|
|--------------|----------------|--------------------------------------|
|数据输入和输出|- 手动输入数据 </br> - 导出数据 </br> - 导入数据 </br> - 加载已训练的模型 </br> - 解压缩已压缩的数据集|- 手动输入数据 </br> - 导出数据 </br> - 导入数据|
|数据格式转换|- 转换为 CSV </br> - 转换为数据集 </br> - 转换为 ARFF </br> - 转换为 SVMLight </br> - 转换为 TSV|- 转换为 CSV </br> - 转换为数据集|
|数据转换 - 操作|- 添加列</br> - 添加行 </br> - 应用 SQL 转换 </br> - 清除丢失的数据 </br> - 转换为指示器值 </br> - 编辑元数据 </br> - 联接数据 </br> - 删除重复的行 </br> - 在数据集中选择列 </br> - 选择列转换 </br> - SMOTE </br> - 组分类值|- 添加列</br> - 添加行 </br> - 应用 SQL 转换 </br> - 清除丢失的数据 </br> - 转换为指示器值 </br> - 编辑元数据 </br> - 联接数据 </br> - 删除重复的行 </br> - 在数据集中选择列 </br> - 选择列转换 </br> - SMOTE|
|数据转换 – 缩放和约简 |- 剪切值 </br> - 将数据分组到箱中 </br> - 规范化数据 </br>- 主体组件分析 |- 剪切值 </br> - 将数据分组到箱中 </br> - 规范化数据|
|数据转换 – 样本和拆分|- 分区和采样 </br> - 拆分数据|- 分区和采样 </br> - 拆分数据|
|数据转换 - 过滤 |- 应用筛选器 </br> - FIR 过滤器 </br> IIR 过滤器 </br> - 中间值过滤器 </br> - 移动平均线过滤器 </br> - 阈值过滤器 </br> - 用户定义的过滤器||
|数据转换 - 通过计数进行学习 |- 构建计数转换 </br> - 导出计数表 </br> - 导入计数表 </br> - 合并计数转换</br>  - 修改计数表参数||
|特征选择 |- 基于筛选器的特征选择 </br> - 费舍尔线性判别分析  </br> - 排列特征重要性 |- 基于筛选器的特征选择 </br>  - 排列特征重要性|
| 模型 - 分类| - 多类决策林 </br> - 多类决策森林  </br> - 多类逻辑回归  </br>- 多类神经网络  </br>-“一对多”多类分类 </br>- 双类平均感知器 </br>- 双类贝叶斯点机 </br>- 双类提升决策树  </br> - 双类决策林  </br> - 双类决策森林  </br> - 双类局部深层 SVM </br> - 双类逻辑回归  </br> - 双类神经网络 </br> - 双类支持向量机  | - 多类决策林 </br>  - 多类提升决策树  </br> - 多类逻辑回归 </br> - 多类神经网络 </br> -“一对多”多类分类  </br> - 双类平均感知器  </br> - 双类提升决策树  </br> - 双类决策林 </br>- 双类逻辑回归 </br> - 双类神经网络 </br>- 双类支持向量机  |
| 模型 - 聚类分析| - K-Means 群集| - K-Means 群集|
| 模型 - 回归| - 贝叶斯线性回归  </br> - 提升决策树回归  </br>- 决策林回归  </br> - 快速林分位数回归  </br> - 线性回归 </br> - 神经网络回归 </br> - 序数回归泊松回归| - 提升决策树回归  </br>- 决策林回归  </br> - 快速林分位数回归 </br> - 线性回归  </br> - 神经网络回归 </br> - 泊松回归|
| 模型 - 异常情况检测| - 单类 SVM  </br> - 基于 PCA 的异常情况检测 | - 基于 PCA 的异常情况检测|
| 机器学习 - 评估  | - 交叉验证模型  </br>- 评估模型  </br>- 评估推荐器 | - 交叉验证模型  </br>- 评估模型 </br> - 评估推荐器|
| 机器学习 - 训练| - 扫描群集  </br> - 训练异常情况检测模型 </br>- 训练聚类分析模型  </br> - 训练 Matchbox 推荐器 -</br> 训练模型  </br>- 优化模型超参数| - 训练异常情况检测模型  </br> - 训练聚类分析模型 </br> - 训练模型 -</br> - 训练 PyTorch 模型  </br>- 训练 SVD 推荐器  </br>- 训练 Wide and Deep 推荐器 </br>- 优化模型超参数|
| 机器学习 - 评分| - 应用转换  </br>- 将数据分配到聚类  </br>- 为 Matchbox 推荐器评分 </br> - 评分模型|- 应用转换  </br> - 将数据分配到聚类 </br> - 为图像模型评分  </br> - 评分模型 </br>- 为 SVD 推荐器评分 </br> \- 为 Wide and Deep 推荐器评分|
| OpenCV 库模块| - 导入图像 </br>- 预先训练的级联图像分类 | |
| Python 语言模块| - 执行 Python 脚本| - 执行 Python 脚本  </br> - 创建 Python 模型 |
| R 语言模块  | - 执行 R 脚本  </br> - 创建 R 模型| - 执行 R 脚本|
| 统计函数 | - 应用数学运算 </br>- 计算基础统计信息  </br>- 计算线性相关性  </br>- 评估概率函数  </br>- 替换离散值  </br>- 汇总数据  </br>- 使用 t 测试来测试假设| - 应用数学运算  </br>- 汇总数据|
| 文本分析| - 检测语言  </br>- 从文本中提取关键短语  </br>- 从文本中提取 N 元语法特征  </br>- 特征哈希 </br>- 隐性 Dirichlet 分配  </br>- 命名实体识别 </br>- 预处理文本  </br>- 为 Vowpal Wabbit 版本 7-10 模型评分  </br>- 为 Vowpal Wabbit 版本 8 模型评分 </br>- 训练 Vowpal Wabbit 版本 7-10 模型  </br>- 训练 Vowpal Wabbit 版本 8 模型 |- 将单词转换为矢量 </br> - 从文本中提取 N 元语法特征 </br>- 特征哈希  </br>- 隐性 Dirichlet 分配 </br>- 预处理文本  </br>- 为 Vowpal Wabbit 模型评分 </br> - 训练 Vowpal Wabbit 模型|
| 时序| - 时序异常情况检测 | |
| Web 服务 | - 输入 </br> - 输出 | - 输入 </br>  - Output（输出）|
| 计算机视觉| | - 应用图像转换 </br> - 转换为图像目录 </br> - 初始化图像转换 </br> - 拆分图像目录  </br> - DenseNet 图像分类   </br>- ResNet 图像分类 |

若要详细了解如何使用各个设计器模块，请查看[设计器模块参考](../algorithm-module-reference/module-reference.md)。

### <a name="what-if-a-designer-module-is-missing"></a>如果缺少设计器模块怎么办？

Azure 机器学习设计器包含工作室（经典）最热门的模块。 它还包括可利用最新机器学习技术的新模块。 

如果由于设计器中缺少模块而阻止了迁移，请通过[创建支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)联系我们。

## <a name="example-migration"></a>迁移示例

以下试验迁移重点介绍了工作室（经典）和 Azure 机器学习之间的一些区别。

### <a name="datasets"></a>数据集

在工作室（经典）中，数据集保存在你的工作区中，仅供工作室（经典）使用。

![automobile-price-classic-dataset](./media/migrate-overview/studio-classic-dataset.png)

在 Azure 机器学习中，数据集注册在工作区中，并且可以在所有 Azure 机器学习中使用。 有关 Azure 机器学习数据集的优势的详细信息，请参阅[安全数据访问](../concept-data.md#reference-data-in-storage-with-datasets)。

![automobile-price-aml-dataset](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>管道

在工作室（经典）中，试验包含工作的处理逻辑。 你已使用拖放模块创建了试验。

![automobile-price-classic-experiment](./media/migrate-overview/studio-classic-experiment.png)

在 Azure 机器学习中，管道包含工作的处理逻辑。 可以使用拖放模块或通过编写代码来创建管道。

![automobile-price-aml-pipeline](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Web 服务终结点

工作室（经典）使用 REQUEST/RESPOND API 进行实时预测，使用 BATCH EXECUTION API 进行批量预测或重新训练 。

![automobile-price-classic-webservice](./media/migrate-overview/studio-classic-web-service.png)

Azure 机器学习使用实时终结点进行实时预测，使用管道终结点进行批量预测或重新训练 。

![automobile-price-aml-endpoint](./media/migrate-overview/aml-endpoint.png)

## <a name="next-steps"></a>后续步骤

本文介绍了迁移到 Azure 机器学习的高级要求。 有关详细步骤，请参阅工作室（经典）迁移系列中的其他文章：

1. 迁移概述。
1. [迁移数据集](migrate-register-dataset.md)。
1. [重新生成工作室（经典）训练管道](migrate-rebuild-experiment.md)。
1. [重新生成工作室（经典）Web 服务](migrate-rebuild-web-service.md)。
1. [将 Azure 机器学习 Web 服务与客户端应用集成](migrate-rebuild-integrate-with-client-app.md)。
1. [迁移执行 R 脚本](migrate-execute-r-script.md)。