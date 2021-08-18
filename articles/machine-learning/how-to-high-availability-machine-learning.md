---
title: 故障转移和灾难恢复
titleSuffix: Azure Machine Learning
description: 了解如何为 Azure 机器学习规划灾难恢复并保持业务连续性。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 05/05/2021
ms.openlocfilehash: ba8904713f9623cf80f259ad096a4dbfaddad393
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112552903"
---
# <a name="failover-for-business-continuity-and-disaster-recovery"></a>业务连续性和灾难恢复的故障转移

为了最大化运行时间，请提前规划以维持业务连续性并使用 Azure 机器学习为灾难恢复做好准备。 

Microsoft 致力于确保 Azure 服务一直可用。 不过，可能会发生计划外服务中断。 我们建议制定灾难恢复计划来处理区域性服务中断。 本文将指导如何进行以下操作：

* 规划 Azure 机器学习和相关资源的多区域部署。
* 针对解决方案的高可用性进行设计。
* 启动到另一个地区的故障转移。

> [!NOTE]
> Azure 机器学习本身不提供自动故障转移或灾难恢复。

## <a name="understand-azure-services-for-azure-machine-learning"></a>了解适用于 Azure 机器学习的 Azure 服务

Azure 机器学习依赖于多个 Azure 服务，并具有多个层。 其中一些服务已在（客户）订阅中预配。 你负责这些服务的高可用性配置。 其他服务在 Microsoft 订阅中创建，并由 Microsoft 管理。 

Azure 服务包括：

* **Azure 机器学习基础结构**：适用于 Azure 机器学习工作区的 Microsoft 托管环境。

* **关联资源**：Azure 机器学习工作区创建期间在订阅中预配的资源。 这些资源包括 Azure 存储、Azure Key Vault、Azure 容器注册表和 Application Insights。 你负责配置这些资源的高可用性设置。
  * 默认存储具有模型、训练日志数据和数据集等数据。
  * Key Vault 具有 Azure 存储、容器注册表和数据存储的凭据。
  * 容器注册表具有用于训练和推理环境的 Docker 映像。
  * Application Insights 用于监视 Azure 机器学习。

* **计算资源**：在部署工作区之后创建的资源。 例如，可能会创建一个计算实例或计算群集来训练机器学习模型。
  * 计算实例和计算群集：Microsoft 托管模型开发环境。
  * 其他资源：可附加到 Azure 机器学习的 Microsoft 计算资源，例如 Azure Kubernetes 服务 (AKS)、Azure Databricks、Azure 容器实例和 Azure HDInsight。 你负责配置这些资源的高可用性设置。

* **其他数据存储**：Azure 机器学习可以装载其他数据存储（例如 Azure 存储、Azure Data Lake Storage 和 Azure SQL 数据库）来训练数据。  这些数据存储已在订阅中预配。 你负责配置它们的高可用性设置。

下表显示由 Microsoft 管理的 Azure 服务和由你管理的服务。 它还指示默认情况下高度可用的服务。

| 服务 | 管理者 | 默认具有高可用性 |
| ----- | ----- | ----- |
| **Azure 机器学习基础结构** | Microsoft | |
| **关联资源** |
| Azure 存储 | 你 | |
| Key Vault | 你 | ✓ |
| 容器注册表 | 你 | |
| Application Insights | 你 | NA |
| **计算资源** |
| 计算实例 | Microsoft |  |
| 计算群集 | Microsoft |  |
| 其他计算资源，例如 AKS、 <br>Azure Databricks、容器实例、HDInsight | 你 |  |
| **其他数据存储**，例如 Azure 存储、SQL 数据库、<br> Azure Database for PostgreSQL、Azure Database for MySQL、 <br>Azure Databricks 文件系统 | 你 | |

本文的其余部分介绍如何使这些服务具有高可用性。

## <a name="plan-for-multi-regional-deployment"></a>多区域部署计划

多区域部署依赖于在两个 Azure 区域中创建 Azure 机器学习和其他资源（基础结构）。 如果发生区域性服务中断，可以切换到另一个区域。 规划资源的部署位置时，请考虑：

* __区域可用性__：使用靠近用户的区域。 若要检查 Azure 机器学习的区域可用性，请参阅 [Azure 产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/)）。
* __Azure 配对区域__：配对区域协调平台更新，并在需要时确定恢复工作的优先级。 有关详细信息，请参阅 [Azure 配对区域](../best-practices-availability-paired-regions.md)。
* __服务可用性__：确定解决方案使用的资源应该是热/热、热/暖还是热/冷。
    
    * __热/热__：两个区域同时处于活动状态，其中有一个区域已准备就绪，可立即使用。
    * __热/暖__：主要区域处于活动状态，次要区域具有可供使用的关键资源（例如，已部署的模型）。 非关键资源需要手动部署在次要区域。
    * __热/冷__：主要区域处于活动状态，次要区域已部署 Azure 机器学习和其他资源，以及所需的数据。 需要手动部署模型、模型部署或管道等资源。

> [!TIP]
> 根据业务要求，可能需要区别对待不同的 Azure 机器学习资源。 例如，对已部署的模型使用热/热（推理），对试验使用热/冷（训练）。

Azure 机器学习基于其他服务进行构建。 某些服务可以配置为复制到其他区域。 其他服务必须在多个区域中手动创建。 下表提供了负责复制的服务列表，以及配置概述：

| Azure 服务 | 异地复制执行者 | Configuration |
| ----- | ----- | ----- |
| 机器学习工作区 | 你 | 在所选区域中创建工作区。 |
| 机器学习计算 | 你 | 在所选区域中创建计算资源。 对于可以动态缩放的计算资源，请确保这两个区域提供的计算配额足以满足你的需求。 |
| 密钥保管库 | Microsoft | 在两个区域中，将相同的 Key Vault 实例与 Azure 机器学习工作区和资源一起使用。 Key Vault 会自动故障转移到次要区域。 有关详细信息，请参阅 [Azure Key Vault 可用性和冗余](../key-vault/general/disaster-recovery-guidance.md)。|
| 容器注册表 | Microsoft | 将容器注册表实例配置为将注册表异地复制到 Azure 机器学习的配对区域。 对两个工作区实例使用相同的实例。 有关详细信息，请参阅 [Azure 容器注册表中的异地复制](../container-registry/container-registry-geo-replication.md)。 |
| 存储帐户 | 你 | Azure 机器学习不支持使用异地冗余存储 (GRS)、异地区域冗余存储 (GZRS)、读取访问异地冗余存储 (RA-GRS) 或读取访问异地区域冗余存储 (RA-GZRS) 的默认存储帐户故障转移。 为每个工作区的默认存储创建一个单独的存储帐户。 </br>为其他数据存储创建单独的存储帐户或服务。 有关详细信息，请参阅 [Azure 存储冗余](../storage/common/storage-redundancy.md)。 |
| Application Insights | 你 | 为两个区域中的工作区创建 Application Insights。 若要调整数据保留期和详细信息，请参阅 [Application Insights 中的数据收集、保留和存储](../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)。 |

若要在次要区域中启用快速恢复和重启，建议使用以下开发实践：

* 使用 Azure 资源管理器模板。 模板是“基础结构即代码”，可让你快速地在这两个区域中部署服务。
* 若要避免两个区域之间发生偏差，请更新持续集成和部署管道，将其部署到这两个区域。
* 自动执行部署时，请包括配置工作区附加计算资源（例如 Azure Kubernetes 服务）。
* 为两个区域的用户创建角色分配。
* 为两个区域创建网络资源，例如 Azure 虚拟网络和专用终结点。 确保用户有权访问这两个网络环境。 例如，这两个虚拟网络的 VPN 和 DNS 配置。

### <a name="compute-and-data-services"></a>计算和数据服务

根据需求，你可能拥有更多由 Azure 机器学习使用的计算或数据服务。 例如，你可能使用 Azure Kubernetes 服务或 Azure SQL 数据库。 使用以下信息了解如何配置这些服务以实现高可用性。

__计算资源__

* **Azure Kubernetes 服务**：请参阅 [Azure Kubernetes 服务 (AKS) 中实现业务连续性和灾难恢复的最佳做法](../aks/operator-best-practices-multi-region.md)和 [创建使用可用性区域的 Azure Kubernetes 服务 (AKS) 群集](../aks/availability-zones.md)。 如果 AKS 群集是使用 Azure 机器学习工作室、SDK 或 CLI 创建的，则不支持跨区域高可用性。
* **Azure Databricks**：请参阅 [Azure Databricks 群集的区域性灾难恢复](/azure/databricks/scenarios/howto-regional-disaster-recovery)。
* **容器实例**：业务流程协调程序负责故障转移。 请参阅 [Azure 容器实例和容器业务流程协调程序](../container-instances/container-instances-orchestrator-relationship.md)。
* **HDInsight**：请参阅 [Azure HDInsight 支持的高可用性服务](../hdinsight/hdinsight-high-availability-components.md)。

__数据服务__

* **Azure Blob 容器/Azure 文件存储/Data Lake Storage Gen2**：请参阅 [Azure 存储冗余](../storage/common/storage-redundancy.md)。
* **Data Lake Storage Gen1**：请参阅 [Data Lake Storage Gen1 的高可用性和灾难恢复指南](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)。
* **SQL 数据库**：请参阅 [Azure SQL 数据库和 SQL 托管实例的高可用性](../azure-sql/database/high-availability-sla.md)。
* **Azure Database for PostgreSQL**：请参阅 [Azure Database for PostgreSQL - 单一服务器中的高可用性概念](../postgresql/concepts-high-availability.md)。
* **Azure Database for MySQL**：请参阅 [了解 Azure Database for MySQL 中的业务连续性](../mysql/concepts-business-continuity.md)。
* **Azure Databricks 文件系统**：请参阅 [Azure Databricks 群集的区域性灾难恢复](/azure/databricks/scenarios/howto-regional-disaster-recovery)。

> [!TIP]
> 如果你提供自己的客户管理的密钥来部署 Azure 机器学习工作区，则还会在订阅中预配 Azure Cosmos DB。 在这种情况下，你应负责配置其高可用性设置。 请参阅[使用 Azure Cosmos DB 实现高可用性](../cosmos-db/high-availability.md)。

## <a name="design-for-high-availability"></a>旨在实现高可用性

### <a name="deploy-critical-components-to-multiple-regions"></a>将关键组件部署到多个区域

确定目标业务连续性级别。 解决方案的组件之间的级别可能有所不同。 例如，对生产管道或模型部署使用热/热配置，对试验使用热/冷配置。

### <a name="manage-training-data-on-isolated-storage"></a>管理独立存储上的训练数据

通过将数据存储与工作区用于日志的默认存储分离，你可以：

* 将与数据存储相同的存储实例附加到主要工作区和次要工作区。
* 对数据存储帐户使用异地复制，并最大限度地延长运行时间。

### <a name="manage-machine-learning-artifacts-as-code"></a>将机器学习项目作为代码进行管理

Azure 机器学习中的运行由运行规范定义。 此规范包括对在工作区实例级别管理的输入项目（包括环境、数据集和计算）的依赖项。 对于多区域运行提交和部署，建议采用以下做法：

* 借助 Git 存储库，在本地管理基本代码。
    * 从 Azure 机器学习工作室中导出重要的笔记本。
    * 导出在工作室中以代码形式编写的管道。

      > [!NOTE]
      > 在工作室设计器中创建的管道当前无法作为代码导出。

* 将配置作为代码进行管理。

    * 避免对工作区进行硬编码引用。 请改为使用[配置文件](how-to-configure-environment.md#workspace)配置对工作区实例的引用，并使用 [Workspace.from_config()](/python/api/azureml-core/azureml.core.workspace.workspace#remarks) 初始化工作区。 若要自动执行此过程，请使用[用于机器学习的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)命令 [az ml folder attach](/cli/azure/ml(v1)/folder#ext_azure_cli_ml_az_ml_folder_attach)。
    * 使用运行提交帮助程序，例如 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 和[管道](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class))。
    * 使用 [Environments.save_to_directory()](/python/api/azureml-core/azureml.core.environment(class)#save-to-directory-path--overwrite-false-) 保存环境定义。
    * 如果使用自定义 Docker 映像，请使用 Dockerfile。
    * 使用 [Dataset](/python/api/azureml-core/azureml.core.dataset(class)) 类定义解决方案使用的数据[路径](/python/api/azureml-core/azureml.data.datapath)的集合。
    * 使用 [Inferenceconfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) 类将模型部署为推理终结点。

## <a name="initiate-a-failover"></a>启动故障转移

### <a name="continue-work-in-the-failover-workspace"></a>在故障转移工作区中继续工作

当主要工作区不可用时，可以切换到次要工作区以继续试验和开发。 如果发生中断，Azure 机器学习不会自动将运行提交给次要工作区。 更新代码配置，使其指向新的工作区资源。 建议避免硬编码工作区引用。 应使用[工作区配置文件](how-to-configure-environment.md#workspace)来最大程度地减少更改工作区时的手动用户步骤。 请确保还将所有自动化（例如持续集成和部署管道）更新到新工作区。

Azure 机器学习无法同步或恢复工作区实例之间的项目或元数据。 根据应用程序部署策略，可能需要移动项目或在故障转移工作区中重新创建试验输入（例如数据集对象）才能继续运行提交。 如果已将主要工作区和次要工作区资源配置为共享启用了异地复制的关联资源，则某些对象可能直接可用于故障转移工作区。 例如，两个工作区共享相同的 docker 映像、配置的数据存储和 Azure Key Vault 资源。 下图显示了一个配置，其中两个工作区共享相同的映像 (1) 、数据存储 (2) 和 Key Vault (3)。

![引用资源配置](./media/how-to-high-availability-machine-learning/bcdr-resource-configuration.png)

> [!NOTE]
> 发生服务中断时运行的任何作业都不会自动转换到次要工作区。 即使解决中断后，作业也不太可能在主要工作区中恢复并成功完成。 相反，必须在次要工作区或主要工作区中重新提交这些作业（中断解决后）。

### <a name="moving-artifacts-between-workspaces"></a>在工作区之间移动项目

根据恢复方法，你可能需要在工作区之间复制数据集和模型对象等项目，才能继续工作。 目前，工作区之间的项目可移植性受到限制。 建议尽可能将项目作为代码管理，以便可以在故障转移实例中重新创建这些项目。

可以使用[机器学习的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)在工作区之间导出和导入以下项目：

| 项目 | 导出 | 导入 |
| ----- | ----- | ----- |
| 模型 | [az ml model download --model-id {ID} --target-dir {PATH}](/cli/azure/ext/azure-cli-ml/ml/model#ext_azure_cli_ml_az_ml_model_download) | [az ml model register –name {NAME} --path {PATH}](/cli/azure/ext/azure-cli-ml/ml/model) |
| 环境 | [az ml environment download -n {NAME} -d {PATH}](/cli/azure/ext/azure-cli-ml/ml/environment#ext_azure_cli_ml_az_ml_environment_download) | [az ml environment register -d {PATH}](/cli/azure/ext/azure-cli-ml/ml/environment#ext_azure_cli_ml_az_ml_environment_register) |
| Azure ML 管道（代码生成工具） | [az ml pipeline get --path {PATH}](/cli/azure/ml(v1)/pipeline#ext_azure_cli_ml_az_ml_pipeline_get) | [az ml pipeline create --name {NAME} -y {PATH}](/cli/azure/ml(v1)/pipeline#ext_azure_cli_ml_az_ml_pipeline_create)

> [!TIP]
> * 无法下载或移动已注册的数据集。 这包括由 Azure ML 生成的数据集，如中间管道数据集。 但是可以在两个工作区上注册以下数据集：引用两个工作区都可以访问的共享文件位置或引用复制基础数据存储的位置的数据集。 使用 [az ml dataset register](/cli/azure/ml(v1)/dataset#ext_azure_cli_ml_az_ml_dataset_register) 注册数据集。
> * 运行输出存储在与工作区关联的默认存储帐户中。 尽管在中断时可能无法从工作室 UI 访问运行输出，但你可以通过存储帐户直接访问数据。 有关处理存储在 blob 中的数据的详细信息，请参阅[使用 Azure CLI 创建、下载和列出 blob](../storage/blobs/storage-quickstart-blobs-cli.md)。
## <a name="next-steps"></a>后续步骤

若要使用具有高可用性设置的关联资源部署 Azure 机器学习，请使用 [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-advanced)。
