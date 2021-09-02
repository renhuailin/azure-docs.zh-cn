---
title: ML 工作室（经典）：迁移到 Azure 机器学习 - 使用管道终结点
description: 在 Azure 机器学习中将管道终结点与客户端应用程序集成。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 5e3b0559a9a86a4e8168e15021d1cadde4acf08f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868293"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>使用客户端应用程序中的管道终结点

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

本文介绍了如何将客户端应用程序与 Azure 机器学习终结点集成。 有关编写应用程序代码的详细信息，请参阅[使用 Azure 机器学习终结点](how-to-consume-web-service.md)。

本文是 Azure 机器学习迁移系列的工作室（经典）的一部分。 有关迁移到 Azure 机器学习的详细信息，请参阅[迁移概述一文](migrate-overview.md)。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure 机器学习工作区。 [创建 Azure 机器学习工作区](how-to-manage-workspace.md#create-a-workspace)。
- [Azure 机器学习实时终结点或管道终结点](migrate-rebuild-web-service.md)。


## <a name="consume-a-real-time-endpoint"></a>使用实时终结点 

如果已将模型部署为实时终结点，则可以在 C#、Python 和 R 中查找其 REST 终结点和预生成的使用代码：

1. 转到 Azure 机器学习工作室 ([ml.azure.com](https://ml.azure.com))。
1. 转到“终结点”选项卡。
1. 选择实时终结点。
1. 选择“使用”。

> [!NOTE]
> 你还可在“详细信息”选项卡中找到终结点的 Swagger 规范。请使用 Swagger 定义来了解终结点架构。 有关 Swagger 定义的详细信息，请参阅 [Swagger 官方文档](https://swagger.io/docs/specification/2-0/what-is-swagger/)。


## <a name="consume-a-pipeline-endpoint"></a>使用管道终结点

有两种使用管道终结点的方法：

- REST API 调用
- 与 Azure 数据工厂集成

### <a name="use-rest-api-calls"></a>使用 REST API 调用

从客户端应用程序调用 REST 终结点。 你可以使用终结点的 Swagger 规范来了解其架构：

1. 转到 Azure 机器学习工作室 ([ml.azure.com](https://ml.azure.com))。
1. 转到“终结点”选项卡。
1. 选择“管道终结点”。
1. 选择管道终结点。
1. 在“管道终结点概述”窗格中，选择“REST 终结点文档”下的链接。

### <a name="use-azure-data-factory"></a>使用 Azure 数据工厂

作为 Azure 数据工厂管道的一个步骤，你可以调用 Azure 机器学习管道。 有关详细信息，请参阅[在 Azure 数据工厂中执行 Azure 机器学习管道](../data-factory/transform-data-machine-learning-service.md)。


## <a name="next-steps"></a>后续步骤

本文介绍了如何查找管道终结点的架构和示例代码。 有关使用来自客户端应用程序的终结点的详细信息，请参阅[使用 Azure 机器学习终结点](how-to-consume-web-service.md)。

请参阅 Azure 机器学习迁移系列中的其他文章： 
1. [迁移概述](migrate-overview.md)。
1. [迁移数据集](migrate-register-dataset.md)。
1. [重新生成工作室（经典）训练管道](migrate-rebuild-experiment.md)。
1. [重新生成工作室（经典）Web 服务](migrate-rebuild-web-service.md)。
1. **将 Azure 机器学习 Web 服务与客户端应用集成**。
1. [迁移执行 R 脚本](migrate-execute-r-script.md)。