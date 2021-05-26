---
title: 教程：使用 Azure 数据工厂从数据中批量提取信息 - 表单识别器
titleSuffix: Azure Applied AI Services
description: 设置 Azure 数据工厂活动来触发表单识别器模型的训练和运行，并将大量文档数字化。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: lajanuar
ms.openlocfilehash: ddb4ec251cc6d630ba8161a6f25bce33223c75cf
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110377861"
---
# <a name="tutorial-extract-form-data-in-bulk-by-using-azure-data-factory"></a>教程：使用 Azure 数据工厂批量提取表单数据

在本教程中，我们会介绍如何使用 Azure 服务将大批表单引入数字媒体。 本教程介绍如何将 Azure Data Lake 文档中的数据自动引入 Azure SQL 数据库。 单击几下即可快速训练模型和处理新的文档。

## <a name="business-need"></a>业务需求

大多数组织现在都认识到了其不同格式（PDF、图像、视频）的数据是多么宝贵。 他们在寻求最佳做法和最经济高效的方式来将这些资产数字化。

此外，我们的客户经常有来自其众多客户端和客户的各种类型的表单。 与[快速入门](./quickstarts/client-library.md)不同，本教程将介绍如何通过元数据驱动的方式，使用新的不同类型的表单自动训练模型。 如果当前没有针对给定表单类型的模型，系统将创建一个，并提供模型 ID。 

通过从表单中提取数据并将其与现有操作系统和数据仓库相结合，企业可获得见解，并向其客户和业务用户提供价值。

Azure 表单识别器可帮助组织使用他们的数据、自动执行流程（发票付款、税务处理等）、节约时间成本，以及提高数据准确性。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置 Azure Data Lake 用于存储表单。
> * 使用 Azure 数据库创建参数化表。
> * 使用 Azure 密钥保管库存储敏感凭据。
> * 在 Azure Databricks 笔记本中训练表单识别器模型。
> * 使用 Databricks 笔记本提取表单数据。
> * 使用 Azure 数据工厂自动训练和提取表单。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 [免费创建一个](https://azure.microsoft.com/free/cognitive-services/)。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="创建表单识别器资源"  target="_blank">创建表单识别器资源 </a>，以获取密钥和终结点。 资源部署后，选择“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到表单识别器 API。 稍后你会在本快速入门中将密钥和终结点粘贴到代码中。
    * 可以使用免费定价层 (F0) 来试用该服务。 然后可以升级到付费层进行生产。
* 至少有五个相同类型的表单。 理想情况是该工作流可支持大量文档。 有关整理训练数据集的提示和选项，请参阅[生成训练数据集](./build-training-data-set.md)。 对于本教程，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2128080)的 Train 文件夹中的文件。


## <a name="project-architecture"></a>项目体系结构 

此项目代表一组 Azure 数据工厂管道，它们用于触发 Python 笔记本，以便在 Azure Data Lake 存储帐户中分析、提取文档中的数据并使用这些数据进行训练。

表单识别器 REST API 要求使用一些参数作为输入内容。 出于安全原因，其中一些参数将存储在 Azure 密钥保管库中。 其他敏感性更低的参数（例如存储 Blob 文件夹名称）将存储在 Azure SQL 数据库中的参数化表中。

对于要分析的每种表单类型，数据工程师或数据科学家将填充参数表的一行。 然后，他们使用 Azure 数据工厂来循环访问检测到的表单类型列表，并将相关参数传递给 Databricks 笔记本来训练或重新训练表单识别器模型。 也可在此处使用 Azure 函数。

然后，Azure Databricks 笔记本使用经过训练的模型来提取表单数据。 它会将该数据导出到 Azure SQL 数据库。

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="显示项目体系结构的示意图。":::


## <a name="set-up-azure-data-lake"></a>设置 Azure Data Lake

你的大量表单可能位于本地环境或 (s)FTP 服务器中。 本教程使用 Azure Data Lake Storage Gen2 帐户中的表单。 可在这里使用 Azure 数据工厂、Azure 存储资源管理器或 AzCopy 来传输文件。 训练数据库和评分数据集可位于不同的容器中，但各表单类型的训练数据集必须在同一容器中。 （它们可在不同的文件夹中。）

若要创建新的数据湖，请按照[创建用于 Azure Data Lake Storage Gen2 的存储帐户](../../storage/blobs/create-data-lake-storage-account.md)中的说明进行操作。

## <a name="create-a-parameterization-table"></a>创建参数化表

接下来，我们将在 Azure SQL 数据库中创建元数据表。 此表将包含表单识别器 REST API 所需的非敏感数据。 每当数据集中有一种新类型的表单时，我们都将在此表中插入一个新记录，并触发训练和评分管道。 （我们稍后将实现这些管道。）

此表中将使用以下字段：

* **form_description**。 在训练期间无需使用此字段。 此字段对我们要为其训练模型的表单类型提供相关说明（例如“客户端 A 表单”、“酒店 B 表单”）。
* **training_container_name**。 存储帐户容器的名称，我们在此容器中存储了训练数据集。 它可以是与 scoring_container_name 相同的容器。
* **training_blob_root_folder**。 存储帐户中的文件夹，我们将在这里存储用于训练模型的文件。
* **scoring_container_name**。 存储帐户容器的名称，我们在此容器中存储了要从中提取键/值对的文件。 它可以是与 training_container_name 相同的容器。
* **scoring_input_blob_folder**。 存储帐户中的文件夹，我们将在这里存储要从中提取数据的文件。
* **model_id**。 要重新训练的模型的 ID。 首次运行时，值必须设置为 -1，这将指示训练笔记本创建一个新的自定义模型进行训练。 训练笔记本会将新模型 ID 返回给 Azure 数据工厂实例。 我们将使用一个存储过程活动在 Azure SQL 数据库中更新此值。

  每当要引入新的表单类型时，都需要先将模型 ID 手动重置为 -1，然后再训练模型。

* **file_type**。 支持的表单类型包括 `application/pdf`、`image/jpeg`、`image/png` 和 `image/tif`。

  如果你有其他文件类型的表单，则在训练新的表单类型时，需要更改此值和 **model_id**。
* **form_batch_group_id**。 随着时间的推移，你可能会有多个要针对同一模型进行训练的表单类型。 借助 **form_batch_group_id** 字段，可以指定已使用特定模型进行训练的所有表单类型。

### <a name="create-the-table"></a>创建表


[创建 Azure SQL 数据库](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase)，然后在[查询编辑器](../../azure-sql/database/connect-query-portal.md)中运行此 SQL 脚本来创建所需的表：


```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

运行此脚本以创建在训练后自动更新 **model_id** 的流程。

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>使用 Azure Key Vault 存储敏感凭据

出于安全原因，我们不希望在 Azure SQL 数据库的参数化表中存储某些敏感信息。 我们会将敏感参数存储为 Azure Key Vault 机密。

### <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault

[创建 Key Vault 资源](https://ms.portal.azure.com/#create/Microsoft.KeyVault)。 然后，在创建后转到密钥保管库资源，并在“设置”部分选择“机密”来添加参数 。

将出现一个新窗口。 选择“生成/导入”。 输入参数名称及其值，然后选择“创建”。 针对以下参数完成这些步骤：

* **CognitiveServiceEndpoint**。 表单识别器 API 的终结点 URL。
* **CognitiveServiceSubscriptionKey**。 表单识别器服务的访问密钥。 
* **StorageAccountName**。 存储帐户，其中存储了要从中提取键/值对的训练数据集和表单。 如果这些项位于不同的帐户，请输入每个帐户名称作为单独的机密。 请记住，无论哪种表单类型，训练数据集都必须在同一容器中。 它们可以位于不同的文件夹中。
* **StorageAccountSasKey**。 存储帐户的共享访问签名 (SAS)。 若要检索 SAS URL，请转到存储资源。 在“存储资源管理器”选项卡上转到你的容器，单击右键并选择“获取共享访问签名”。  请务必获取容器的 SAS，而不是存储帐户本身的。 确保选中“读取”和“列出”权限，然后选择“创建”。   然后复制 **URL** 部分中的值。 它应采用以下格式：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>在 Databricks 笔记本中训练表单识别器模型

你将使用 Azure Databricks 来存储和运行与表单识别器服务进行交互的 Python 代码。

### <a name="create-a-notebook-in-databricks"></a>在 Databricks 中创建笔记本

在 Azure 门户中[创建 Azure Databricks 资源](https://ms.portal.azure.com/#create/Microsoft.Databricks)。 创建后转到该资源，然后打开工作区。

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>创建 Azure Key Vault 支持的机密范围


若要引用前面创建的 Azure 密钥保管库中的机密，需要在 Databricks 中创建一个机密范围。 请按照[创建 Azure 密钥保管库支持的机密范围](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)中的步骤进行操作。

### <a name="create-a-databricks-cluster"></a>创建 Databricks 群集

群集是一组 Databricks 计算资源。 创建群集：

1. 在左窗格中选择“群集”按钮。
1. 在“群集”页上选择“创建群集” 。
1. 在“创建群集”页上指定群集名称，然后在“Databricks Runtime 版本”列表中选择“7.2 (Scala 2.12，Spark 3.0.0)”  。
1. 选择“创建群集”。

### <a name="write-a-settings-notebook"></a>写入设置笔记本

现已准备好添加 Python 笔记本了。 首先创建一个名为“设置”的笔记本。 此笔记本会将参数化表中的值分配给脚本中的变量。 Azure 数据工厂稍后将以参数的形式传递这些值。 我们还会将密钥保管库中机密的值分配给变量。 

1. 若要创建“设置”笔记本，请选择“工作区”按钮。  在新选项卡上选择下拉列表，选择依次“创建”、“笔记本”。 
1. 在弹出窗口中输入笔记本的名称，然后选择“Python”作为默认语言。 选择你的 Databricks 群集，然后选择“创建”。
1. 在第一个笔记本单元中，我们会检索由 Azure 数据工厂传递的参数：

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. 在第二个单元中，我们会从密钥保管库中检索机密，并将它们分配给变量：

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>写入训练笔记本

现在我们已完成“设置”笔记本，接下来可创建一个笔记本来训练模型了。 如上所述，我们将使用在 Azure Data Lake Storage Gen2 帐户 (**training_blob_root_folder**) 中的文件夹内存储的文件。 文件夹名称已作为变量传入。 每组表单类型将在同一个文件夹中。 在我们循环访问参数表时，将使用所有表单类型来训练模型。 

1. 创建名为 **TrainFormRecognizer** 的笔记本。 
1. 在第一个单元中，运行“设置”笔记本：

    ```python
    %run "./Settings"
    ```

1. 在下一个单元中，分配来自“设置”文件的变量，并针对每种表单类型动态训练模型，应用 REST 快速入门中的代码。

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # If you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key/value pairs.
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# If you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. 训练过程的最后一步是以 JSON 格式获取训练结果：

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-by-using-a-notebook"></a>使用笔记本提取表单数据

### <a name="mount-the-azure-data-lake-storage"></a>装载 Azure Data Lake 存储

接下来是使用经过训练的模型对我们拥有的各种表单进行评分。 我们将在 Databricks 中装载 Azure Data Lake Storage 帐户，并在引入过程中引用该装载。

就像在训练阶段一样，我们将使用 Azure 数据工厂调用从表单中提取键/值对的操作。 我们将在参数表中指定的文件夹内循环访问表单。

1. 创建笔记本，用于在 Databricks 中装载存储帐户。 将其命名为 **MountDataLake**。 
1. 首先，需要调用“设置”笔记本：

    ```python
    %run "./Settings"
    ```

1. 在第二个单元中为敏感参数（我们将从密钥保管库机密中检索这些参数）定义变量：

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. 尝试卸载存储帐户（如果之前装载过）：

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. 装载存储帐户：


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > 我们仅装载了训练存储帐户。 在这种情况下，训练文件以及我们想要从中提取键/值对的文件位于同一存储帐户中。 如果你的评分存储帐户和训练存储帐户不相同，则需要装载这两个存储帐户。 

### <a name="write-the-scoring-notebook"></a>写入评分笔记本

现在我们可以创建一个评分笔记本。 与在训练笔记本中的操作类似：我们将使用刚才装载的 Azure Data Lake Storage 帐户中的文件夹内存储的文件。 文件夹名称以变量的形式传递。 我们将循环访问指定文件夹中的所有表单，并从中提取键/值对。 

1. 创建一个笔记本，并将其命名为 **ScoreFormRecognizer**。 
1. 运行“设置”和 **MountDataLake**  笔记本：

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. 添加以下代码用于调用[分析](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) API：

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. 在下一个单元中，我们将获取键/值对提取操作的结果。 此单元将输出结果。 我们需要 JSON 格式的结果，以便在 Azure SQL 数据库或 Azure Cosmos DB 中对其做进一步的处理。 因此我们将结果写入某个 .json 文件。 输出文件名称将与“_output.json”串联用作已评分的文件的名称。 该文件将与源文件存储在同一文件夹中。

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. 在新单元中执行文件写入过程：

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-by-using-azure-data-factory"></a>使用 Azure 数据工厂自动训练和评分

接下来只需要设置 Azure 数据工厂服务来自动执行训练和评分过程即可。 首先，按照[创建数据工厂](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)中的步骤操作。 创建 Azure 数据工厂资源后，需要创建三个管道：一个用于训练，两个用于评分。 （稍后将予以说明。）

### <a name="training-pipeline"></a>训练管道

在训练管道中，第一个活动是“查找”，目的是读取和返回 Azure SQL 数据库中参数化表内的值。 所有训练数据集将在同一存储帐户和容器中（但可能在不同的文件夹中）。 因此，我们将保留查找活动设置中的默认特性值“仅限第一行”。 对于要针对其训练模型的每种表单类型，我们将使用 **training_blob_root_folder** 中的所有文件来训练模型。

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="显示在数据工厂中训练管道的屏幕截图。":::

该存储过程采用两个参数：**model_id** 和 **form_batch_group_id**。 用于从 Databricks 笔记本返回模型 ID 的代码为 `dbutils.notebook.exit(model_id)`。 用于在数据工厂的存储过程活动中读取代码的代码为 `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id`。

### <a name="scoring-pipelines"></a>评分管道

为了提取键/值对，我们将扫描参数化表中的所有文件夹。 对于每个文件夹，我们将提取其中所有文件的键/值对。 Azure 数据工厂目前不支持嵌套的 ForEach 循环。 此处我们将创建两个管道。 第一个管道将在参数化表中进行查找，并以参数形式将文件夹列表传递到第一个管道。

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="显示数据工厂中第一个评分管道的屏幕截图。":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="显示数据工厂中第一个评分管道详细信息的屏幕截图。":::

第二个管道使用 GetMeta 活动获取文件夹中的文件列表，并将它作为参数传递给 Databricks 评分笔记本。

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="显示数据工厂中第二个评分管道的屏幕截图。":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="显示数据工厂中第二个评分管道详细信息的屏幕截图。":::

### <a name="specify-a-degree-of-parallelism"></a>指定并行度

在训练管道和评分管道中都可指定并行度，以便能够同时处理多个表单。

若要在 Azure 数据工厂管道中设置并行度：

1. 选择“ForEach”活动。
1. 清除“顺序”框。
1. 在“批处理计数”框中设置并行度。 对于评分，建议批处理计数不超过 15。

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="显示 Azure 数据工厂中评分活动的并行度配置的屏幕截图。":::

## <a name="how-to-use-the-pipeline"></a>如何使用管道

现在你有一个自定义管道，来将你的大量表单数字化并在其基础上运行一些分析。 向现有存储文件夹添加类型相似的新表单时，只需重新运行评分管道即可。 它们将更新你的所有输出文件，包括新表单的输出文件。 

如果添加具有新类型的新表单，那么还需要将训练数据集上传到相应的容器中。 接下来，在参数化表中添加一个新行，输入新文档的位置及其训练数据集。 为 **model_ID** 输入值 -1，以指示需要针对这些表单训练新的模型。 然后在 Azure 数据工厂中运行训练管道。 该管道将从表中读取信息、训练模型，并覆盖表中的模型 ID。 然后，可以调用评分管道，以开始写入输出文件。

## <a name="next-steps"></a>后续步骤

在本教程中，你已设置 Azure 数据工厂管道来触发表单识别器模型的训练和运行，并将大量文件数字化。 接下来，探索表单识别器 API，了解你可用它执行的其他操作。

* [表单识别器 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)
