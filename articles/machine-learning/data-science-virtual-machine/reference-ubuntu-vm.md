---
title: 参考：Ubuntu Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine 中包含的工具的详细信息
author: timoklimmer
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
ms.author: tklimmer
ms.date: 05/12/2021
ms.topic: reference
ms.openlocfilehash: d2a960b2a924d06b7ecc2a2f613fdd5a5695071a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110076377"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>参考：Ubuntu (Linux) Data Science Virtual Machine

有关 Ubuntu Data Science Virtual Machine 上可用工具的列表，请参见下文。 

## <a name="deep-learning-libraries"></a>深度学习库

### <a name="pytorch"></a>PyTorch

[PyTorch](https://pytorch.org/) 是广泛支持机器学习算法的常用科学计算框架。 如果计算机内置了 GPU，它可利用该 GPU 来加速深度学习。PyTorch 在 `py38_pytorch` 环境中可用。

### <a name="h2o"></a>H2O

H2O 是一种快速的内存中分布式机器学习和预测分析平台。 根环境和 py35 Anaconda 环境中都安装有 Python 包。 同时也会安装 R 包。 

若要从命令行打开 H2O，请运行 `java -jar /dsvm/tools/h2o/current/h2o.jar`。 可能需要配置各种[命令行选项](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line)。 若要开始，可以浏览到 `http://localhost:54321` 以访问 Flow Web UI。 示例笔记本也位于 JupyterHub。

### <a name="tensorflow"></a>TensorFlow

[TensorFlow](https://tensorflow.org) 是 Google 的深度学习库。 它是使用数据流图进行数值计算的开源软件库。 如果计算机内置了 GPU，它可利用该 GPU 来加速深度学习。 TensorFlow 在 `py38_tensorflow` conda 环境中可用。


## <a name="python"></a>Python

DSVM 预安装了多个 Python 环境，其中的 Python 版本为 Python 3.8 或 Python 3.6。
若要查看已安装的环境的完整列表，请在命令行中运行 `conda env list`。


## <a name="jupyter"></a>Jupyter

DSVM 还附带了 Jupyter，它是用于共享代码和分析的环境。 Jupyter 会以不同风格安装在 DSVM 上：
 - Jupyter Lab
 - Jupyter 笔记本
 - Jupyter 中心

若要打开 Jupyter 实验室，请从应用程序菜单中打开 Jupyter，或单击桌面图标。 也可通过从命令行运行 `jupyter lab` 来打开 Jupyter 实验室。

若要打开 Jupyter 笔记本，请打开命令行并运行 `jupyter notebook`。

若要打开 Jupyter 中心，请打开 https://\<VM DNS name or IP address\>:8000/。 然后，需要提供本地 Linux 用户名和密码。

> [!NOTE]
> 如果收到任何证书警告，请选择继续。

> [!NOTE]
> 对于 Ubuntu 映像，在预配了 VM 时，防火墙中会默认打开端口 8000。


## <a name="apache-spark-standalone"></a>Apache Spark 独立版

一个 Apache Spark 独立版实例已预装在 Linux DSVM 上，以帮助你在本地开发 Spark 应用程序，然后在大型群集上对其进行测试和部署。 

可以通过 Jupyter 内核运行 PySpark 程序。 打开 Jupyter 时，选择“新建”按钮即可看到可用内核的列表。 “Spark - Python”是 PySpark 内核。借助它可以使用 Python 语言生成 Spark 应用程序。 还可以使用 Python IDE（如 VS.Code 或 PyCharm）来生成 Spark 程序。 

在此独立实例中，Spark 堆栈会在调用方客户端程序中运行。 与在 Spark 群集上进行开发相比，使用此功能可以更快、更轻松地排查问题。


## <a name="ides-and-editors"></a>IDE 和编辑器

有多种代码编辑器可供选择，包括 VS.Code、PyCharm、RStudio、IntelliJ、vi/Vim、Emacs。 

VS.Code、PyCharm、RStudio 和 IntelliJ 是图形编辑器。 若要使用它们，需要登录到图形桌面。 可以使用桌面和应用程序菜单中的快捷方式打开它们。

Vim 和 Emacs 是基于文本的编辑器。 Emacs 上的 ESS 附加包可以简化 Emacs 编辑器中 R 的处理。 可在 [ESS 网站](https://ess.r-project.org/)上找到更多信息。


## <a name="databases"></a>数据库

### <a name="graphical-sql-client"></a>图形化 SQL 客户端

SQuirrel SQL 是一个图形化 SQL 客户端，可连接到各种数据库（如 Microsoft SQL Server 和 MySQL）和运行 SQL 查询。 打开 SQuirrel SQL 的最快方式是从图形桌面会话中使用应用程序菜单（例如通过 X2Go 客户端来这样做）

首次使用前，需设置驱动程序和数据库别名。 JDBC 驱动程序位于 /usr/share/java/jdbcdrivers 中。

有关详细信息，请参阅 [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)。

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>用于访问 Microsoft SQL Server 的命令行工具

SQL Server 的 ODBC 驱动程序包还附带两个命令行工具：

- **bcp**：bcp 工具在 Microsoft SQL Server 实例与用户指定格式的数据文件之间批量复制数据。 可以使用 bcp 工具将大量新行导入 SQL Server 表，或者将表中的数据导出到数据文件。 要将数据导入表中，必须使用为该表创建的格式文件。 或者，必须了解表的结构，以及对其列有效的数据类型。

有关详细信息，请参阅[使用 bcp 连接](/sql/connect/odbc/linux-mac/connecting-with-bcp)。

- **sqlcmd**：可以使用 sqlcmd 工具输入 Transact-SQL 语句。 还可以在命令提示符下输入系统过程和脚本文件。 此工具使用 ODBC 运行 Transact-SQL 批处理。

  有关详细信息，请参阅[使用 sqlcmd 连接](/sql/connect/odbc/linux-mac/connecting-with-sqlcmd)。

  > [!NOTE]
  > 此工具在 Linux 和 Windows 平台之间存在差异。 有关详细信息，请参阅文档。

### <a name="database-access-libraries"></a>数据库访问库

在 R 和 Python 中可以使用库来访问数据库：

* 在 R 中，可以使用 RODBC 包或 dplyr 包在数据库服务器上查询或运行 SQL 语句。
* 在 Python 中，pyodbc 库提供使用 ODBC 作为基础层的数据库访问。  

## <a name="azure-tools"></a>Azure 工具

VM 上安装有以下 Azure 工具：

* **Azure CLI**：可以使用 Azure 中的命令行接口通过 shell 命令创建和管理 Azure 资源。 若要打开 Azure 工具，请输入 **azure help**。 有关详细信息，请参阅 [Azure CLI 文档页](/cli/azure/get-started-with-az-cli2)。
* **Azure 存储资源管理器**：Azure 存储资源管理器是一个图形工具，用于浏览在 Azure 存储帐户中存储的对象，以及将数据上传到 Azure Blob 和从中下载数据。 可通过桌面快捷方式图标访问存储资源管理器。 还可以通过输入 **StorageExplorer** 从 shell 提示符打开此工具。 必须从 X2Go 客户端登录，或设置 X11 转发。
* **Azure 库**：下面是一些预安装的库。
  
  * **Python**：Python 中的 Azure 相关库包括 *azure*、*azureml*、*pydocumentdb* 和 *pyodbc*。 使用前三个库，可以访问 Azure 存储服务、Azure 机器学习和 Azure Cosmos DB（Azure 上的 NoSQL 数据库）。 通过第 4 个库 pyodbc（以及 SQL Server 的 Microsoft ODBC 驱动程序），可使用 ODBC 接口从 Python 访问 SQL Server、Azure SQL 数据库和 Azure Synapse Analytics。 输入 **pip 列表** 查看所有列出的库。 请确保在 Python 2.7 和 3.5 环境中都运行此命令。
  * **R**：R 中的 Azure 相关库包括 AzureML 和 RODBC。
  * **Java**：可在 VM 上的 /dsvm/sdk/AzureSDKJava 目录中找到 Azure Java 库列表。 密钥库是 Azure 存储和用于 SQL Server 的管理 API、Azure Cosmos DB 和 JDBC 驱动程序。  

## <a name="azure-machine-learning"></a>Azure 机器学习

Azure 机器学习是完全托管的云服务，允许构建、部署和共享预测分析解决方案。 可以在 Azure 机器学习工作室（预览）中构建试验和模型。 可从 Data Science Virtual Machine 上的 Web 浏览器，通过访问 [Microsoft Azure 机器学习](https://ml.azure.com)来访问 Azure 机器学习工作室。

登录到 Azure 机器学习工作室后，可以使用试验画布来生成机器学习算法的逻辑流。 还可以访问在 Azure 机器学习上托管的 Jupyter 笔记本，并且可以无缝使用 Azure 机器学习工作室中的试验。 

通过将已构建的机器学习模型包装在 Web 服务接口中，来对它们执行操作。 实施机器学习模型使得以任何语言编写的客户端都能从这些模型中调用预测。 有关详细信息，请参阅[机器学习文档](https://azure.microsoft.com/documentation/services/machine-learning/)。

还可以在 VM 上的 R 或 Python 中生成模型，然后在 Azure 机器学习中将其部署到生产环境。 我们已在 R (**AzureML**) 和 Python (**azureml**) 中分别安装了库以启用此功能。

> [!NOTE]
> 这些说明专为 Windows 版 Data Science Virtual Machine 编写。 但是其中提供的有关将模型部署到 Azure 机器学习的信息也适用于 Linux VM。

## <a name="machine-learning-tools"></a>机器学习工具

VM 随附一些已预编译并已在本地预装的机器学习工具和算法。 其中包括：

* **Vowpal Wabbit**：一种快速的在线学习算法。
* **xgboost**：提供经过优化的提升树算法的工具。
* **Rattle**：基于 R 的图形工具，可用于简单的数据浏览和建模。
* **Python**：Anaconda Python 附带机器学习算法，这些算法含有库（如 Scikit-learn）。 可以通过使用 `pip install` 命令安装其他库。
* **LightGBM**：快速、分布式、高性能的梯度提升框架，基于决策树算法。
* **R**：有丰富的机器学习函数库可供 R 使用。预装的库包括 lm、glm、randomForest 和 rpart。 可运行以下命令安装其他库：

    ```r
    install.packages(<lib name>)
    ```

以下是一些关于列表中前三个机器学习工具的附加信息。

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit 是一种使用在线、哈希、allreduce、缩减、learning2search、主动和交互式学习等技术的机器学习系统。

若要在基本示例上运行该工具，请使用以下命令：

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

该目录中存在其他更大的演示。 有关 Vowpal Wabbit 的详细信息，请参阅 [GitHub 的此部分](https://github.com/JohnLangford/vowpal_wabbit)以及 [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)。

### <a name="xgboost"></a>xgboost

xgboost 库是为提升（树）算法设计和优化的库。 此库的目标是将计算机的计算限制推向极致，以满足提供可缩放、可移植且精确的大规模树提升的需求。

xgboost 作为命令行和 R 库提供。 若要在 R 中使用此库，可以启动交互式 R 会话（在 shell 中输入 **R**），然后加载该库。

下面是可以在 R 提示符中运行的一个简单示例：

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

若要运行 xgboost 命令行，下面是要在 shell 中运行的命令：

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

一个 .model 文件将写入到指定的目录。 可在 [GitHub](https://github.com/dmlc/xgboost/tree/master/demo/CLI/binary_classification) 上找到有关此演示示例的信息。

有关 xgboost 的详细信息，请参阅 [xgboost 文档页](https://xgboost.readthedocs.org/en/latest/)及其 [GitHub 存储库](https://github.com/dmlc/xgboost)。

### <a name="rattle"></a>Rattle

Rattle (R Analytical Tool To Learn Easily) 使用基于 GUI 的数据浏览和建模     。 它提供数据的统计和可视化摘要，转换可轻松建模的数据，从数据构建不受监督和受监督的模型，以图形方式呈现模型的性能，以及对新数据集进行评分。 它还生成 R 代码，用于复制 UI 中可直接在 R 中运行或用作进一步分析的起点的操作。

若要运行 Rattle，需进入图形桌面登录会话。 在终端中输入 **R** 打开 R 环境。 在 R 提示符中，输入以下命令：

```R
library(rattle)
rattle()
```

此时会打开包含一组选项卡的图形界面。 在 Rattle 中执行以下快速入门步骤，使用示例天气数据集并生成模型。 在某些步骤中，系统会提示自动安装并加载尚未安装在系统上的某些必需 R 包。

> [!NOTE]
> 如果无权在系统目录（默认）中安装包，可能会在 R 控制台窗口中看到一个提示，提醒将包安装到个人库中。 如果看到这些提示，请回复 y。

1. 选择“执行”。
1. 此时会显示一个对话框，询问是否要使用示例气象数据集。 选择“是”以加载示例。
1. 选择“模型”选项卡。
1. 选择“执行”以生成决策树。
1. 选择“绘制”以显示决策树。
1. 选择“林”选项，然后选择“执行”以生成随机林。 
1. 选择“评估”选项卡。
1. 选择“风险”选项，然后选择“执行”以显示两个“风险(累积)”性能绘图。  
1. 选择“日志”选项卡以显示为上述操作生成的 R 代码。
   （由于当前版本 Rattle 中的 bug，需在日志文本中的“导出此日志”前插入 **#** 字符。）
1. 选择“导出”按钮，将名为 *weather_script.R* 的 R 脚本文件保存到主文件夹。

可以退出 Rattle 和 R。现在，可以修改生成的 R 脚本。 或者，可以按原样使用该脚本，并可随时运行它来重复 Rattle UI 中的所有操作。 尤其是对于 R 初学者而言，使用此方法可在简单的图形界面中快速执行分析和机器学习，同时在 R 中自动生成代码来修改项目或用于学习。

## <a name="next-steps"></a>后续步骤

还有其他问题吗？ 请考虑创建[支持票证](https://azure.microsoft.com/support/create-ticket/)。