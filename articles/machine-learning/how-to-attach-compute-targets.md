---
title: 设置训练和推理计算目标
titleSuffix: Azure Machine Learning
description: 将计算资源（计算目标）添加到工作区，用于机器学习训练和推理。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 06/18/2021
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1
ms.openlocfilehash: d6a50982b546ae5d4e615c2e3cfc2b2dcd955a0f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713243"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>设置模型训练和部署的计算目标

了解如何将 Azure 计算资源附加到 Azure 机器学习工作区。  然后，可以将这些资源用作机器学习任务中的训练和推理[计算目标](concept-compute-target.md)。

本文介绍如何将工作区设置为使用这些计算资源：

* 本地计算机
* 远程虚拟机
* Apache Spark 池（由 Azure Synapse Analytics 提供支持）
* Azure HDInsight
* Azure Batch
* Azure Databricks - 仅在[机器学习管道](how-to-create-machine-learning-pipelines.md)中用作训练计算目标
* Azure Data Lake Analytics
* Azure 容器实例
* Azure Kubernetes 服务和启用了 Azure Arc 的 Kubernetes（预览版）

若要使用 Azure 机器学习管理的计算目标，请参阅：

* [Azure 机器学习计算实例](how-to-create-manage-compute-instance.md)
* [Azure 机器学习计算群集](how-to-create-attach-compute-cluster.md)
* [Azure Kubernetes 服务群集](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[Azure 机器学习 Python SDK](/python/api/overview/azure/ml/intro) 或 [Azure 机器学习 Visual Studio Code 扩展](how-to-setup-vs-code.md)。

## <a name="limitations"></a>限制

* 请勿在工作区中为同一计算创建多个同步附件。 例如，使用两个不同的名称将一个 Azure Kubernetes 服务群集附加到工作区。 每个新附件都会破坏先前存在的附件。

    如果要重新附加计算目标来实现某个目的（例如，更改 TLS 或其他群集配置设置），则必须先删除现有附件。

## <a name="whats-a-compute-target"></a>什么是计算目标？

使用 Azure 机器学习，可在不同的资源或环境（统称为[计算目标](concept-azure-machine-learning-architecture.md#compute-targets)）中训练模型。 计算目标可以是本地计算机，也可以是云资源，例如 Azure 机器学习计算、Azure HDInsight 或远程虚拟机。  还可以使用计算目标进行模型部署，如[部署模型的位置和方式](how-to-deploy-and-where.md)中所述。


## <a name="local-computer"></a><a id="local"></a>本地计算机

使用本地计算机进行训练时，无需创建计算目标。  只需从本地计算机[提交训练运行](how-to-set-up-training-targets.md)。

使用本地计算机进行推理时，必须安装 Docker。 若要执行部署，请使用 [LocalWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#deploy-configuration-port-none-) 来定义 Web 服务将使用的端口。 然后使用[通过 Azure 机器学习部署模型](how-to-deploy-and-where.md)中所述的常规部署流程。

## <a name="remote-virtual-machines"></a><a id="vm"></a>远程虚拟机

Azure 机器学习还支持连接 Azure 虚拟机。 VM 必须是 Azure Data Science Virtual Machine (DSVM)。 此 VM 提供精选的工具和框架用于满足整个机器学习开发生命周期的需求。 有关如何将 DSVM 与 Azure 机器学习配合使用的详细信息，请参阅[配置开发环境](./how-to-configure-environment.md#dsvm)。

> [!TIP]
> 建议使用 [Azure 机器学习计算实例](concept-compute-instance.md)，而不是远程 VM。 它是一个完全托管的基于云的计算解决方案，专用于 Azure 机器学习。 有关详细信息，请参阅[创建和管理 Azure 机器学习计算实例](how-to-create-manage-compute-instance.md)。

1. **创建**：Azure 机器学习无法为你创建远程 VM。 你需要自行创建 VM，然后将其附加到 Azure 机器学习工作区。 要详细了解如何创建 DSVM，请参阅[预配适用于 Linux (Ubuntu) 的 Data Science Virtual Machine](./data-science-virtual-machine/dsvm-ubuntu-intro.md)。

    > [!WARNING]
    > Azure 机器学习仅支持运行 Ubuntu 的虚拟机。 创建 VM 或选择现有 VM 时，必须选择使用 Ubuntu 的 VM。
    > 
    > Azure 机器学习还要求虚拟机具有公共 IP 地址。

1. **附加**：若要附加现有虚拟机作为计算目标，必须提供虚拟机的资源 ID、用户名和密码。 可以使用订阅 ID、资源组名称和 VM 名称按以下字符串格式构造 VM 的资源 ID：`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   或者，可以[使用 Azure 机器学习工作室](how-to-create-attach-compute-studio.md#attached-compute)将 DSVM 附加到工作区。

    > [!WARNING]
    > 请勿在工作区中为同一 DSVM 创建多个同步附件。 每个新附件都会破坏先前存在的附件。

1. **配置**：为 DSVM 计算目标创建运行配置。 Docker 与 conda 用于在 DSVM 上创建和配置训练环境。

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

> [!TIP]
> 如果要从工作区中删除（拆离）VM，请使用 [RemoteCompute.detach()](/python/api/azureml-core/azureml.core.compute.remotecompute#detach--) 方法。
>
> Azure 机器学习不会为你删除 VM。 必须使用 Azure 门户、CLI 或适用于 Azure VM 的 SDK 手动删除 VM。

## <a name="apache-spark-pools"></a><a id="synapse"></a>Apache Spark 池

通过 Azure Synapse Analytics 与 Azure 机器学习的集成（预览版），你可以附加由 Azure Synapse 提供支持的 Apache Spark 池，以进行交互式数据探索和准备。 借助这种集成，你可使用专用计算大规模地进行数据整理。 有关详细信息，请参阅[如何附加由 Azure Synapse Analytics 提供支持的 Apache Spark 池](how-to-link-synapse-ml-workspaces.md#attach-synapse-spark-pool-as-a-compute)。

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight 是用于大数据分析的热门平台。 该平台提供的 Apache Spark 可用于训练模型。

1. **创建**：Azure 机器学习无法为你创建 HDInsight 群集。 你需要自行创建群集，然后将其附加到 Azure 机器学习工作区。 有关详细信息，请参阅[在 HDInsight 中创建 Spark 群集](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)。 

    > [!WARNING]
    > Azure 机器学习要求 HDInsight 群集具有公共 IP 地址。

    创建群集时，必须指定 SSH 用户名和密码。 请记下这些值，因为在将 HDInsight 用作计算目标时需要用到这些值。
    
    创建群集后，使用主机名 \<clustername>-ssh.azurehdinsight.net 连接到该群集，其中，\<clustername> 是为该群集提供的名称。 

1. **附加**：若要将 HDInsight 群集附加为计算目标，必须提供该 HDInsight 群集的资源 ID、用户名和密码。 可以使用订阅 ID、资源组名称和 HDInsight 群集名称按以下字符串格式构造 HDInsight 群集的资源 ID：`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   或者，可以[使用 Azure 机器学习工作室](how-to-create-attach-compute-studio.md#attached-compute)将 HDInsight 群集附加到工作区。

    > [!WARNING]
    > 请勿在工作区中为同一 HDInsight 创建多个同步附件。 每个新附件都会破坏先前存在的附件。

1. **配置**：为 HDI 计算目标创建运行配置。 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]

> [!TIP]
> 如果要从工作区中删除（拆离）HDInsight 群集，请使用 [HDInsightCompute.detach()](/python/api/azureml-core/azureml.core.compute.hdinsight.hdinsightcompute#detach--) 方法。
>
> Azure 机器学习不会为你删除 HDInsight 群集。 必须使用 Azure 门户、CLI 或适用于 Azure HDInsight 的 SDK 将其手动删除。

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch 用于在云中高效运行大规模并行高性能计算 (HPC) 应用程序。 可以在 Azure 机器学习管道中使用 AzureBatchStep 将作业提交到 Azure Batch 计算机池。

若要将 Azure Batch 附加为计算目标，必须使用 Azure 机器学习 SDK 并提供以下信息：

-    **Azure Batch 计算名称**：在工作区中用于计算的易记名称
-    **Azure Batch 帐户名称**：Azure Batch 帐户的名称
-    **资源组**：包含 Azure Batch 帐户的资源组。

以下代码演示如何将 Azure Batch 附加为计算目标：

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> 请勿在工作区中为同一 Azure Batch 创建多个同步附件。 每个新附件都会破坏先前存在的附件。

## <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks 是 Azure 云中基于 Apache Spark 的环境。 它可以用作 Azure 机器学习管道的计算目标。

> [!IMPORTANT]
> Azure 机器学习无法创建 Azure Databricks 计算目标。 而必须由你自行创建一个 Azure Databricks 工作区，然后将其附加到 Azure 机器学习工作区。 若要创建工作区资源，请参阅[在 Azure Databricks 中运行 Spark 作业](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)文档。
> 
> 若要从不同 Azure 订阅附加 Azure Databricks 工作区，你（你的 Azure AD 帐户）必须被授予 Azure Databricks 工作区上的“参与者”角色。 查看 [Azure 门户](https://ms.portal.azure.com/)中的访问权限。

要将 Azure Databricks 附加为计算目标，请提供以下信息：

* __Databricks 计算名称__：要分配给此计算资源的名称。
* __Databricks 工作区名称__：Azure Databricks 工作区的名称。
* __Databricks 访问令牌__：用于对 Azure Databricks 进行身份验证的访问令牌。 若要生成访问令牌，请参阅[身份验证](/azure/databricks/dev-tools/api/latest/authentication)文档。

以下代码演示如何使用 Azure 机器学习 SDK 将 Azure Databricks 附加为计算目标：

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

有关更详细的示例，请参阅 GitHub 上的 [示例笔记本](https://aka.ms/pl-databricks)。

> [!WARNING]
> 请勿在工作区中为同一 Azure Databricks 创建多个同步附件。 每个新附件都会破坏先前存在的附件。

## <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics 是 Azure 云中的大数据分析平台。 它可以用作 Azure 机器学习管道的计算目标。

使用该平台之前，请先创建 Azure Data Lake Analytics 帐户。 若要创建此资源，请参阅 [Azure Data Lake Analytics 入门](../data-lake-analytics/data-lake-analytics-get-started-portal.md)文档。

若要将 Data Lake Analytics 附加为计算目标，必须使用 Azure 机器学习 SDK 并提供以下信息：

* __计算名称__：要分配给此计算资源的名称。
* __资源组__：包含 Data Lake Analytics 帐户的资源组。
* __帐户名称__：Data Lake Analytics 帐户名。

以下代码演示如何将 Data Lake Analytics 附加为计算目标：

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

有关更详细的示例，请参阅 GitHub 上的 [示例笔记本](https://aka.ms/pl-adla)。

> [!WARNING]
> 请勿在工作区中为同一 ADLA 创建多个同步附件。 每个新附件都会破坏先前存在的附件。

> [!TIP]
> Azure 机器学习管道只能处理 Data Lake Analytics 帐户的默认数据存储中存储的数据。 如果需要处理的数据不在默认存储中，可以在训练之前使用 [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep) 复制数据。

## <a name="azure-container-instance"></a><a id="aci"></a>Azure 容器实例

Azure 容器实例 (ACI) 是在部署模型时动态创建的。 不能以任何其他方式创建 ACI 和将其附加到工作区。 有关详细信息，请参阅[将模型部署到 Azure 容器实例](how-to-deploy-azure-container-instance.md)。

## <a name="kubernetes-preview"></a><a id="kubernetes"></a>Kubernetes（预览版）

Azure 机器学习提供了以下选项来附加你自己的 Kubernetes 群集进行训练：

* [Azure Kubernetes 服务](../aks/intro-kubernetes.md)。 Azure Kubernetes 服务在 Azure 中提供托管群集。
* [Azure Arc Kubernetes](../azure-arc/kubernetes/overview.md)。 如果群集托管在 Azure 外部，请使用已启用 Azure Arc 的 Kubernetes 群集。

[!INCLUDE [arc-enabled-machine-learning-create-training-compute](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

若要从工作区分离 Kubernetes 群集，请使用以下方法：

```python
compute_target.detach()
```

> [!WARNING]
> 分离群集不会删除群集。 若要删除 Azure Kubernetes 服务群集，请参阅[将 Azure CLI 与 AKS 配合使用](../aks/kubernetes-walkthrough.md#delete-the-cluster)。 若要删除已启用 Azure Arc 的 Kubernetes 群集，请参阅 [Azure Arc 快速入门](../azure-arc/kubernetes/quickstart-connect-cluster.md#7-clean-up-resources)。

## <a name="notebook-examples"></a>Notebook 示例

请参阅以下 Notebook，其中提供了有关使用各种计算目标进行训练的示例：
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* 使用计算资源来[配置并提交训练运行](how-to-set-up-training-targets.md)。
* [教程：训练模型](tutorial-train-models-with-aml.md)使用一个托管计算目标来训练模型。
* 若要构建更好的模型，请了解如何[高效地优化超参数](how-to-tune-hyperparameters.md)。
* 训练模型后，了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。
* [通过 Azure 虚拟网络使用 Azure 机器学习](./how-to-network-security-overview.md)
