---
title: 快速入门：链接 Azure 机器学习工作区
description: 将 Synapse 工作区链接到 Azure 机器学习工作区
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 10/01/2021
author: nelgson
ms.author: negust
ms.openlocfilehash: 6c7ec83fcf7ea03dcacc92137f1ef63a6cce1f2f
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399520"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>快速入门：在 Synapse 中创建新的 Azure 机器学习链接服务

在本快速入门中，你需要将 Azure Synapse Analytics 工作区链接到 Azure 机器学习工作区。 通过链接这些工作区，可以利用 Synapse 中的各种体验来使用 Azure 机器学习。

例如，链接到 Azure 机器学习工作区可获得以下体验：

- 将 Azure 机器学习管道作为 Synapse 管道中的一个步骤运行。 若要了解详细信息，请参阅[执行 Azure 机器学习管道](../../data-factory/transform-data-machine-learning-service.md)。

- 通过从 Azure 机器学习模型注册表中引入机器学习模型，可使用预测来丰富数据，并在 Synapse SQL 池中对该模型进行评分。 有关更多详细信息，请参阅[教程：用于 Synapse SQL 池的机器学习模型评分向导](tutorial-sql-pool-model-scoring-wizard.md)。

## <a name="two-types-of-authentication"></a>两种类型的身份验证
在 Azure Synapse 中创建 Azure 机器学习链接服务时，可以使用两种类型的标识。
* Synapse 工作区托管标识
* Service Principal

以下部分提供了有关如何使用这两种不同类型的身份验证创建 Azure 机器学习链接服务的指导。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。
- [Synapse Analytics 工作区](../get-started-create-workspace.md)，其中 ADLS Gen2 存储帐户配置为默认存储。 你必须是所用 ADLS Gen2 文件系统的存储 Blob 数据参与者。
- [Azure 机器学习工作区](../../machine-learning/how-to-manage-workspace.md)。
- 如果你选择使用服务主体，则需要拥有相应的权限（或者从拥有相应权限的人员请求这些权限），以便能够创建可用于创建链接服务的服务主体和机密。 请注意，需要为此服务主体分配 Azure 机器学习工作区中的参与者角色。
- 登录到 [Azure 门户](https://portal.azure.com/)

## <a name="create-a-linked-service-using-the-synapse-workspace-managed-identity"></a>使用 Synapse 工作区托管标识创建链接服务

本部分将引导你使用 [Azure Synapse 工作区托管标识](../security/synapse-workspace-managed-identity.md)在 Azure Synapse 中创建 Azure 机器学习链接服务

### <a name="give-msi-permission-to-the-azure-ml-workspace"></a>授予对 Azure ML 工作区的 MSI 权限

1. 在 Azure 门户中导航到你的 Azure 机器学习工作区资源，然后选择“访问控制”

1. 创建一个角色分配，并将你的 Synapse 工作区托管服务标识 (MSI) 添加为 Azure 机器学习工作区的参与者。 请注意，必须是 Azure 机器学习工作区所属资源组的所有者才能执行此操作。 如果在查找 Synapse 工作区 MSI 时遇到问题，请搜索 Synapse 工作区的名称。

### <a name="create-an-azure-ml-linked-service"></a>创建 Azure ML 链接服务

1. 在要新建 Azure 机器学习链接服务的 Synapse 工作区中，转到“管理” > “链接服务”，创建一个类型为“Azure 机器学习”的新链接服务 。

   ![创建链接服务](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. 填写表单：

   - 提供有关要链接到的 Azure 机器学习工作区的详细信息。 这包括有关订阅和工作区名称的详细信息。
   
   - 选择身份验证方法：托管标识
  
3. 单击“测试连接”以验证配置是否正确。 如果连接测试通过，请单击“保存”。

   如果连接测试失败，请确保 Azure Synapse 工作区 MSI 有权访问此 Azure 机器学习工作区，然后重试。

## <a name="create-a-linked-service-using-a-service-principal"></a>使用服务主体创建链接服务

本部分将引导你使用服务主体创建 Azure 机器学习链接服务。

### <a name="create-a-service-principal"></a>创建服务主体

此步骤将创建新的服务主体。 如果要使用现有服务主体，则可以跳过此步骤。

1. 打开 Azure 门户。 

1. 转到“Azure Active Directory” -> “应用注册”。 

1. 单击“新建注册”。 然后，按照说明注册新的应用程序。

1. 注册应用程序后，为应用程序生成机密。 转到你的应用程序  -> “证书和密码”。 单击“添加客户端密码”以生成密码。 请妥善保管密码，稍后将用到它。

   ![生成密码](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. 为应用程序创建服务主体。 转到你的应用程序  -> “概述”，然后单击“创建服务主体”。 在某些情况下，系统会自动创建此服务主体。

   ![创建服务主体](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. 将服务主体添加为 Azure 机器学习工作区的“参与者”。 请注意，必须是 Azure 机器学习工作区所属资源组的所有者才能执行此操作。

   ![分配参与者角色](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

### <a name="create-an-azure-ml-linked-service"></a>创建 Azure ML 链接服务

1. 在要新建 Azure 机器学习链接服务的 Synapse 工作区中，转到“管理” -> “链接服务”，创建一个类型为“Azure 机器学习”的新链接服务。

   ![创建链接服务](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. 填写表单：

   - 提供有关要链接到的 Azure 机器学习工作区的详细信息。 这包括有关订阅和工作区名称的详细信息。

   - 选择身份验证方法：服务主体

   - 服务主体 ID：这是应用程序的应用程序（客户端）ID。

   > [!NOTE]
   > 此 ID 不是应用程序的名称。 你可以在应用程序的概述页中找到此 ID。 它应是一个类似于“81707eac-ab38-406u-8f6c-10ce76a568d5”的长字符串。

   - 服务主体密钥：在上一部分中生成的密钥。

3. 单击“测试连接”以验证配置是否正确。 如果连接测试通过，请单击“保存”。

   如果连接测试失败，请确保服务主体 ID 和密码正确无误，然后重试。

## <a name="next-steps"></a>后续步骤

- [教程：机器学习模型评分向导 - 专用 SQL 池](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics 中的机器学习功能](what-is-machine-learning.md)
