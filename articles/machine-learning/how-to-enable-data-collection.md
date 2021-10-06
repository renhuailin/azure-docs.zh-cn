---
title: 在生产模型上收集数据
titleSuffix: Azure Machine Learning
description: 了解如何从 Azure Kubernetes 服务 (AKS) 群集上部署的 Azure 机器学习模型中收集数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: how-to
ms.custom: data4ml
ms.openlocfilehash: a629b2ca07b9b807ba42e4e071b7f9430ace2108
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427564"
---
# <a name="collect-data-from-models-in-production"></a>收集生产中模型的数据

本文演示如何从 Azure Kubernetes 服务 (AKS) 群集上部署的 Azure 机器学习模型中收集数据， 然后将收集的数据存储在 Azure Blob 存储中。

启用收集后，收集的数据可帮助你：

* 针对收集的生产数据[监视数据偏移](how-to-monitor-datasets.md)。

* 使用 [Power BI](#powerbi) 或 [Azure Databricks](#databricks) 分析收集的数据

* 更好地决定何时重新训练或优化模型。

* 使用收集的数据重新训练模型。

## <a name="what-is-collected-and-where-it-goes"></a>收集哪些数据，收集的数据存储在何处

可以收集以下数据：

* 从部署在 AKS 群集中的 Web 服务收集模型输入数据。 不收集语音、音频、图像和视频数据。
  
* 使用生产输入数据进行模型预测。

>[!NOTE]
> 基于此数据的预先聚合与预先计算目前不是收集服务的一部分。

输出保存在 Blob 存储中。 由于数据将添加到 Blob 存储，因此你可以选择喜好的工具来运行分析。

Blob 中输出数据的路径遵循以下语法：

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> 在低于 0.1.0a16 的适用于 Python 的 Azure 机器学习 SDK 版本中，`designation` 参数命名为 `identifier`。 如果使用早期版本开发代码，则需要相应地更新此名称。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

- 必须安装一个 Azure 机器学习工作区、一个包含脚本的本地目录以及适用于 Python 的 Azure 机器学习 SDK。 若要了解如何安装，请参阅[如何配置开发环境](how-to-configure-environment.md)。

- 需要一个已训练的机器学习模型，该模型将部署到 AKS。 如果没有模型，请参阅[训练图像分类模型](tutorial-train-models-with-aml.md)教程。

- 需要一个 AKS 群集。 有关如何创建此类群集并部署到其中的信息，请参阅[部署方式和部署位置](how-to-deploy-and-where.md)。

- [设置环境](how-to-configure-environment.md)并安装 [Azure 机器学习监视 SDK](/python/api/overview/azure/ml/install)。

## <a name="enable-data-collection"></a>启用数据收集

无论通过 Azure 机器学习或其他工具部署的模型是什么，都可以启用[数据收集](/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector)。

若要启用数据收集，需要：

1. 打开评分文件。

1. 在该文件顶部，添加以下代码：

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. 在 `init` 函数中声明数据集合变量：

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3", "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* 是可选参数。 如果模型不需要此参数，则无需使用它。 使用 *CorrelationId* 确实可以帮助你轻松映射到其他数据，例如 *LoanNumber* 或 *CustomerId*。
    
    稍后将使用 *Identifier* 参数在 Blob 中生成文件夹结构。 可以使用此参数将原始数据与已处理的数据区分开来。

1. 将以下代码行添加到 `run(input_df)` 函数：

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our prediction data into Azure Blob
    ```

1. 在 AKS 中部署服务时，数据收集不会自动设置为 **true**。 如以下示例所示更新配置文件：

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    也可以通过更改以下配置来为 Application Insights 启用服务监视：

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. 若要创建新映像并部署机器学习模型，请参阅[部署方式和部署位置](how-to-deploy-and-where.md)。

1. 将“Azure-Monitoring”pip 包添加到 Web 服务环境的 conda 依赖项：
  ```Python
    env = Environment('webserviceenv')
    env.python.conda_dependencies = CondaDependencies.create(conda_packages=['numpy'],pip_packages=['azureml-defaults','azureml-monitoring','inference-schema[numpy-support]'])
  ```


## <a name="disable-data-collection"></a>禁用数据收集

随时可以停止收集数据。 使用 Python 代码禁用数据收集。

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>验证并分析数据

可以选择偏好的工具来分析收集到 Blob 存储中的数据。

### <a name="quickly-access-your-blob-data"></a>快速访问 Blob 数据

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 打开你的工作区。

1. 选择“存储”。

    [![选择“存储”选项](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Blob 输出数据的路径遵循以下语法：

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a> 使用 Power BI 分析模型数据

1. 下载并打开 [Power BI Desktop](https://www.powerbi.com)。

1. 选择“获取数据”，然后选择“[Azure Blob 存储](/power-bi/desktop-data-sources)”。 

    [![Power BI Blob 设置](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. 添加存储帐户名称并输入存储密钥。 可以通过在 Blob 中选择“设置” > “访问密钥”找到此信息。 

1. 选择“模型数据”容器，然后选择“编辑”。 

    [![Power BI Navigator](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 在查询编辑器中，单击“名称”列的下面，并添加存储帐户。

1. 在筛选器中输入模型路径。 如果只想查看特定年份或月份的文件，则只需展开筛选器路径即可。 例如，如果只想查看三月份的数据，请使用以下筛选路径：

   /modeldata/\<subscriptionid>/\<resourcegroupname>/\<workspacename>/\<webservicename>/\<modelname>/\<modelversion>/\<designation>/\<year>/3

1. 基于“名称”值筛选相关的数据。 如果存储了预测和输入，则需要针对每个预测和输入创建一个查询。

1. 选择“内容”列标题旁边的向下双箭头，将文件合并在一起。

    [![Power BI 内容](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. 选择“确定” 。 数据将预先加载。

    [![Power BI 合并文件](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. 选择“关闭并应用”。

1. 如果添加了输入和预测，则表会自动按 **RequestId** 值排序。

1. 开始基于模型数据生成自定义报表。

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> 使用 Azure Databricks 分析模型数据

1. 创建一个 [Azure Databricks 工作区](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)。

1. 转到该 Databricks 工作区。

1. 在 Databricks 工作区中，选择“上传数据”。

    [![选择 Databricks“上传数据”选项](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. 选择“创建新表”，然后选择“其他数据源” > “Azure Blob 存储” > “在笔记本中创建表”。   

    [![Databricks 表创建](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 更新数据的位置。 以下是示例：

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks 设置](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. 遵循模板中的步骤查看和分析数据。

## <a name="next-steps"></a>后续步骤

针对已收集的数据[检测数据偏移](how-to-monitor-datasets.md)。
