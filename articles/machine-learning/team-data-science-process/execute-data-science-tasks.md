---
title: 执行数据科学任务 - Team Data Science Process
description: 数据科学家如何以可跟踪的协作型版本控制方式执行数据科学项目。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 75166321eba4df5408fb6fc0fbb3119263d532ab
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112464604"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>执行数据科学任务：探索、建模和部署

典型的数据科学任务包括数据探索、建模和部署。 本文概述了如何完成一些常见的数据科学任务，例如交互式数据探索、数据分析、报告和建模。 若要将模型部署到生产环境，可使用以下方法：

- [Azure 机器学习](../index.yml)
- [包含机器学习服务的 SQL-Server](/sql/advanced-analytics/r/r-services)
- [Azure Synapse Analytics](/azure/synapse-analytics/spark/apache-spark-machine-learning-mllib-notebook)


## <a name="1--exploration"></a>1.<a name='DataQualityReportUtility-1'></a>浏览 

数据科学家可通过各种方法执行探索和报告：使用适用于 Python 的库和包（例如 matplotlib）或使用 R（例如 ggplot 或 lattice）。 数据科学家可以自定义此类代码，以满足特定方案中数据探索的需要。 结构化数据与非结构化数据（例如文本或图像）的处理需求有所不同。 

Azure 机器学习等产品还提供了[高级数据准备](../how-to-create-register-datasets.md)用于数据整理和探索，包括特征创建。 用户应确定哪种工具、库和包最适合其需要。 

完成此阶段后，交付件是一份数据探索报告。 该报告应提供相当全面的数据视图用于建模，并评估这些数据是否适合继续在建模步骤中使用。 

## <a name="2--modeling"></a>2.<a name='ModelingUtility-2'></a>建模

有大量的工具包和程序包可用于训练各种语言的模型。 只要符合相关业务用例和生产方案的性能、准确度和延迟要求，数据科学家就可以任意使用他们顺手的工具。

### <a name="model-management"></a>模型管理
生成多个模型后，往往需要配置一个系统来注册和管理这些模型。 通常，需要创建脚本或 API 的组合，以及后端数据库或版本控制系统。 可考虑用于这些管理任务的几个选项包括：

1. [Azure 机器学习 - 模型管理服务](../index.yml)
2. [MIT 提供的 ModelDB](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
3. [用作模型管理系统的 SQL-Server](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)

## <a name="3--deployment"></a>3.<a name='Deployment-3'></a>部署

生产部署可让模型在企业中发挥积极作用。 所部署模型提供的预测可用于业务决策。

### <a name="production-platforms"></a>生产平台
可通过多种方法和平台将模型投入生产。 下面是几个选项：


- [Azure 机器学习中的模型部署](../how-to-deploy-and-where.md)
- [SQL-Server 中的模型部署](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)

> [!NOTE]
> 在部署之前，必须确保模型的延迟评分够低，使模型可在生产环境中使用。
>
>

演练中提供的其他示例演示了适用于 **具体方案** 的操作过程的所有步骤。 [示例演练](walkthroughs.md)一文列出了相关步骤并以缩略图说明的形式提供了链接。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。

> [!NOTE]
> 对于使用 Azure 机器学习工作室的部署，请参阅[部署 Azure 机器学习 Web 服务](../classic/deploy-a-machine-learning-web-service.md)。
>
>

### <a name="ab-testing"></a>A/B 测试
如果在生产环境中部署了多个模型，执行 [A/B 测试](https://en.wikipedia.org/wiki/A/B_testing)来比较模型的性能可能很有用。 

 
## <a name="next-steps"></a>后续步骤

[跟踪数据科学项目的进度](track-progress.md)介绍了数据科学家可通过哪些方式跟踪数据科学项目的进度。

[模型操作和 CI/CD](ci-cd-flask.md) 展示如何使用开发模型执行 CI/CD。
