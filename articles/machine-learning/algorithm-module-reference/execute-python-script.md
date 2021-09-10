---
title: 执行 Python 脚本：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习设计器中的“执行 Python 脚本”模块来运行 Python 代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: devx-track-python, has-adal-ref
author: likebupt
ms.author: keli19
ms.date: 06/15/2021
ms.openlocfilehash: fe98144b204c0baa22bc17972162799b6f341675
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831357"
---
# <a name="execute-python-script-module"></a>“执行 Python 脚本”模块

本文介绍 Azure 机器学习设计器中的“执行 Python 脚本”模块。

使用此模块可以运行 Python 代码。 有关 Python 体系结构和设计原理的详细信息，请参阅[如何在 Azure 机器学习设计器中运行 Python 代码](../how-to-designer-python.md)。

使用 Python，可以执行现有模块不支持的任务，例如：

+ 使用 `matplotlib` 将数据可视化。
+ 使用 Python 库枚举工作区中的数据集和模型。
+ 从[导入数据](./import-data.md)模块不支持的源中读取、加载和操作数据。
+ 运行自己的深度学习代码。 

## <a name="supported-python-packages"></a>支持的 Python 包

Azure 机器学习使用 Python 的 Anaconda 分发版，其中包括用于数据处理的许多常用实用工具。 我们将自动更新 Anaconda 版本。 当前版本为：
 -  Python 3.6 的 Anaconda 4.5 + 分发版 

有关完整列表，请参阅[预安装的 Python 包](#preinstalled-python-packages)部分。

若要安装不在预安装列表中的包（例如 scikit-misc），请将以下代码添加到脚本中： 

```python
import os
os.system(f"pip install scikit-misc")
```

使用以下代码来安装包，以便提高性能（尤其是推理方面）：
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> 如果管道包含的多个“执行 Python 脚本”模块需要使用预安装列表中未包含的包，请在每个模块中安装这些包。

> [!WARNING]
> “执行 Python 脚本”模块不支持使用“apt-get”之类的命令安装依赖于其他本机库的包，例如 Java、PyODBC 等。这是因为，此模块是在仅预安装了 Python 并且具有非管理员权限的简单环境中执行的。  

## <a name="access-to-current-workspace-and-registered-datasets"></a>访问当前工作区和已注册的数据集

可以参阅以下示例代码，在工作区中[访问已注册的数据集](../how-to-create-register-datasets.md)：

```Python
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')
    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    #access to current workspace
    ws = run.experiment.workspace

    #access to registered dataset of current workspace
    from azureml.core import Dataset
    dataset = Dataset.get_by_name(ws, name='test-register-tabular-in-designer')
    dataframe1 = dataset.to_pandas_dataframe()
     
    # If a zip file is connected to the third input port,
    # it is unzipped under "./Script Bundle". This directory is added
    # to sys.path. Therefore, if your zip file contains a Python file
    # mymodule.py you can import it using:
    # import mymodule

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
```

## <a name="upload-files"></a>上传文件
“执行 Python 脚本”支持使用 [Azure 机器学习 Python SDK](/python/api/azureml-core/azureml.core.run%28class%29#upload-file-name--path-or-stream-) 上传文件。

以下示例演示如何将映像文件上传到“执行 Python 脚本”模块中的运行记录：

```Python

# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # For example:
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

管道运行完成后，可以在模块的右侧面板中预览图像。

> [!div class="mx-imgBorder"]
> ![预览已上传的图像](media/module/upload-image-in-python-script.png)

你还可以使用以下代码将文件上传到任何数据存储。 只能预览存储帐户中的文件。
```Python
import pandas as pd

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be None.
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    import os

    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"

    # Set path
    path = "./img_folder"
    os.mkdir(path)
    plt.savefig(os.path.join(path,img_file))

    # Get current workspace
    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    ws = run.experiment.workspace
    
    # Get a named datastore from the current workspace and upload to specified path
    from azureml.core import Datastore 
    datastore = Datastore.get(ws, datastore_name='workspacefilestore')
    datastore.upload(path)

    return dataframe1,
```


## <a name="how-to-configure-execute-python-script"></a>如何配置“执行 Python 脚本”

“执行 Python 脚本”模块包含可用作起点的示例 Python 代码。 若要配置“执行 Python 脚本”模块，请在“Python 脚本”文本框中提供要运行的一组输入和 Python 代码。

1. 将 **执行 Python 脚本** 模块添加到管道。

2. 从设计器中，在 **Dataset1** 上添加并连接要用于输入的任何数据集。 在 Python 脚本中将此数据集引用为 **DataFrame1**。

    数据集的使用是可选的。 如果要使用 Python 生成数据，或者使用 Python 代码将数据直接导入到模块中，则可以使用数据集。

    此模块支持在 Dataset2 上添加另一个数据集。 在 Python 脚本中将第二个数据集引用为 DataFrame2。

    使用此模块加载时，存储在 Azure 机器学习中的数据集将自动转换为 pandas 数据帧。

    ![执行 Python 输入映射](media/module/python-module.png)

4. 若要包括新的 Python 包或代码，请将包含这些自定义资源的压缩文件连接到脚本绑定端口。 或者，如果脚本大于 16 KB，请使用脚本绑定端口以避免错误，如命令行超过 16597 个字符的限制。 

    
    1. 将脚本和其他自定义资源捆绑到一个 zip 文件中。
    1. 将 zip 文件作为“文件数据集”上传到工作室。 
    1. 从设计器创作页面左侧模块窗格的“数据集”列表中拖取数据集模块。 
    1. 将数据集模块连接到“执行 Python 脚本”模块的“脚本包”端口 。
    
    在管道执行期间，可以使用已上传的压缩存档中包含的任何文件。 如果存档中包含目录结构，则会保留结构。
 
    > [!IMPORTANT]
    > 对于脚本捆绑包中的文件，请使用唯一且有意义的名称，因为针对内置服务保留了某些常用词（例如 `test`、`app` 等）。
   
    下面是一个脚本绑定示例，其中包含一个 python 脚本文件和一个 txt 文件：
      
    > [!div class="mx-imgBorder"]
    > ![脚本绑定示例](media/module/python-script-bundle.png)  

    下面是 `my_script.py` 的内容：

    ```python
    def my_func(dataframe1):
    return dataframe1
    ```
    以下是示例代码，显示了如何使用脚本绑定中的文件：    

    ```python
    import pandas as pd
    from my_script import my_func
 
    def azureml_main(dataframe1 = None, dataframe2 = None):
 
        # Execution logic goes here
        print(f'Input pandas.DataFrame #1: {dataframe1}')
 
        # Test the custom defined python function
        dataframe1 = my_func(dataframe1)
 
        # Test to read custom uploaded files by relative path
        with open('./Script Bundle/my_sample.txt', 'r') as text_file:
            sample = text_file.read()
    
        return dataframe1, pd.DataFrame(columns=["Sample"], data=[[sample]])
    ```

5. 在“Python 脚本”文本框中，键入或粘贴有效的 Python 脚本。

    > [!NOTE]
    >  编写脚本时请细致谨慎。 确保没有语法错误，例如使用了未声明的变量或未导入的模块或函数。 请特别注意预安装的模块列表。 若要导入未列出的模块，请在脚本中安装相应的包，例如：
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    “Python 脚本”文本框中预先填充了注释中的某些说明，以及用于数据访问和输出的示例代码。 你必须编辑或替换此代码。 遵循有关缩进和大小写的 Python 约定：

    + 脚本必须包含名为 `azureml_main` 的函数作为此模块的入口点。
    + 入口点函数必须有两个输入参数 `Param<dataframe1>` 和 `Param<dataframe2>`，即使脚本中没有使用这些参数。
    + 连接到第三个输入端口的压缩文件将被解压缩并存储在目录 `.\Script Bundle` 中，该目录还会添加到 Python `sys.path` 中。 

    如果 zip 文件包含 `mymodule.py`，请使用 `import mymodule` 导入它。

    可以向设计器返回两个数据集，数据集必须是 `pandas.DataFrame` 类型的序列。 可以在 Python 代码中创建其他输出，并将其直接写入到 Azure 存储。

    > [!WARNING]
    > 建议不要在“执行 Python 脚本”模块中连接到数据库或其他外部存储。  可以使用[“导入数据”模块](./import-data.md)和[“导出数据”模块](./export-data.md)     

6. 提交管道。

    如果该模块已完成，请检查输出是否符合预期。

    如果该模块发生故障，则需要进行一些故障排除。 选择该模块，然后在右侧窗格中打开“输出+日志”。 打开 70_driver_log.txt，并搜索“in azureml_main”，然后可以找到导致错误的行。 例如，“File "/tmp/tmp01_ID/user_script.py", line 17, in azureml_main”表示错误发生在 python 脚本的第 17 行。

## <a name="results"></a>结果

嵌入的 Python 代码的任何计算结果都必须以 `pandas.DataFrame` 形式提供，该格式将自动转换为 Azure 机器学习数据集格式。 然后可以将结果与管道中的其他模块一起使用。

此模块返回两个数据集：  
  
+ 结果数据集 1，由 Python 脚本中返回的第一个 pandas 数据帧定义。

+ 结果数据集 2，由 Python 脚本中返回的第二个 pandas 数据帧定义。

## <a name="preinstalled-python-packages"></a>预安装的 Python 包
预安装的包如下：
-    adal==1.2.2
-    applicationinsights==0.11.9
-    attrs==19.3.0
-    azure-common==1.1.25
-    azure-core==1.3.0
-    azure-graphrbac==0.61.1
-    azure-identity==1.3.0
-    azure-mgmt-authorization==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    azure-mgmt-keyvault==2.2.0
-    azure-mgmt-resource==8.0.1
-    azure-mgmt-storage==8.0.0
-    azure-storage-blob==1.5.0
-    azure-storage-common==1.4.2
-    azureml-core==1.1.5.5
-    azureml-dataprep-native==14.1.0
-    azureml-dataprep==1.3.5
-    azureml-defaults==1.1.5.1
-    azureml-designer-classic-modules==0.0.118
-    azureml-designer-core==0.0.31
-    azureml-designer-internal==0.0.18
-    azureml-model-management-sdk==1.0.1b6.post1
-    azureml-pipeline-core==1.1.5
-    azureml-telemetry==1.1.5.3
-    backports.tempfile==1.0
-    backports.weakref==1.0.post1
-    boto3==1.12.29
-    botocore==1.15.29
-    cachetools==4.0.0
-    certifi==2019.11.28
-    cffi==1.12.3
-    chardet==3.0.4
-    click==7.1.1
-    cloudpickle==1.3.0
-    configparser==3.7.4
-    contextlib2==0.6.0.post1
-    cryptography==2.8
-    cycler==0.10.0
-    dill==0.3.1.1
-    distro==1.4.0
-    docker==4.2.0
-    docutils==0.15.2
-    dotnetcore2==2.1.13
-    flask==1.0.3
-    fusepy==3.0.1
-    gensim==3.8.1
-    google-api-core==1.16.0
-    google-auth==1.12.0
-    google-cloud-core==1.3.0
-    google-cloud-storage==1.26.0
-    google-resumable-media==0.5.0
-    googleapis-common-protos==1.51.0
-    gunicorn==19.9.0
-    idna==2.9
-    imbalanced-learn==0.4.3
-    isodate==0.6.0
-    itsdangerous==1.1.0
-    jeepney==0.4.3
-    jinja2==2.11.1
-    jmespath==0.9.5
-    joblib==0.14.0
-    json-logging-py==0.2
-    jsonpickle==1.3
-    jsonschema==3.0.1
-    kiwisolver==1.1.0
-    liac-arff==2.4.0
-    lightgbm==2.2.3
-    markupsafe==1.1.1
-    matplotlib==3.1.3
-    more-itertools==6.0.0
-    msal-extensions==0.1.3
-    msal==1.1.0
-    msrest==0.6.11
-    msrestazure==0.6.3
-    ndg-httpsclient==0.5.1
-    nimbusml==1.6.1
-    numpy==1.18.2
-    oauthlib==3.1.0
-    pandas==0.25.3
-    pathspec==0.7.0
-    pip==20.0.2
-    portalocker==1.6.0
-    protobuf==3.11.3
-    pyarrow==0.16.0
-    pyasn1-modules==0.2.8
-    pyasn1==0.4.8
-    pycparser==2.20
-    pycryptodomex==3.7.3
-    pyjwt==1.7.1
-    pyopenssl==19.1.0
-    pyparsing==2.4.6
-    pyrsistent==0.16.0
-    python-dateutil==2.8.1
-    pytz==2019.3
-    requests-oauthlib==1.3.0
-    requests==2.23.0
-    rsa==4.0
-    ruamel.yaml==0.15.89
-    s3transfer==0.3.3
-    scikit-learn==0.22.2
-    scipy==1.4.1
-    secretstorage==3.1.2
-    setuptools==46.1.1.post20200323
-    six==1.14.0
-    smart-open==1.10.0
-    urllib3==1.25.8
-    websocket-client==0.57.0
-    werkzeug==0.16.1
-    wheel==0.34.2

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。