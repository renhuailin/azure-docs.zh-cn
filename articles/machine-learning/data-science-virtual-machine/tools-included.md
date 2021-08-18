---
title: Azure Data Science Virtual Machine 包含的工具
titleSuffix: Azure Data Science Virtual Machine
description: Windows 和 Ubuntu DSVM 映像中包含的工具列表
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: reference
ms.date: 05/12/2021
ms.custom: contperf-fy20q4
ms.openlocfilehash: ba7006c9dc266819e4c965ae3a72fc0f78506056
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649979"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine 中包含哪些工具？

Data Science Virtual Machine 是在云中浏览数据和进行机器学习的一种简单方法。 Data Science Virtual Machine 已预先配置了完整的操作系统、安全修补程序、驱动程序和常用的数据科学和开发软件。 可以选择硬件环境，选择范围包括低成本的以 CPU 为中心的计算机，以及具有多个 GPU、NVMe 存储和大量内存的极强大计算机。 对于具有 GPU 的计算机，我们安装了所有驱动程序，对所有机器学习框架进行了版本匹配以确保 GPU 兼容性，并且在支持 GPU 的所有应用程序软件中启用了加速。

Data Science Virtual Machine 预装有最实用的数据科学工具。 

## <a name="build-deep-learning-and-machine-learning-solutions"></a>构建深度学习和机器学习解决方案

| 工具 | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 使用注意事项 |
|--|:-:|:-:|:-:|
| [CUDA、cuDNN、NVIDIA 驱动程序](https://developer.nvidia.com/cuda-toolkit) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM 上的 CUDA、cuDNN、NVIDIA 驱动程序](./dsvm-tools-deep-learning-frameworks.md#cuda-cudnn-nvidia-driver) |
| [Horovod](https://github.com/horovod/horovod) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [DSVM 上的 Horovod](./dsvm-tools-deep-learning-frameworks.md#horovod) |
| [NVidia System Management Interface  (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [DSVM 上的 nvidia-smi](./dsvm-tools-deep-learning-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| [PyTorch](https://pytorch.org) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [DSVM 上的 PyTorch](./dsvm-tools-deep-learning-frameworks.md#pytorch) |
| [TensorFlow](https://www.tensorflow.org) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM 上的 TensorFlow](./dsvm-tools-deep-learning-frameworks.md#tensorflow) |
| 与 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/) (Python) 集成 | <span class='green-check'>&#9989;</span></br> （Python SDK、示例） | <span class='green-check'>&#9989;</span></br> （Python SDK、CLI、示例） | [Azure ML SDK](./dsvm-tools-data-science.md#azure-machine-learning-sdk-for-python) |
| [XGBoost](https://github.com/dmlc/xgboost) | <span class='green-check'>&#9989;</span></br> （CUDA 支持） | <span class='green-check'>&#9989;</span></br> （CUDA 支持） | [DSVM 上的 XGBoost](./dsvm-tools-data-science.md#xgboost) |
| [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> | [DSVM 上的 Vowpal Wabbit](./dsvm-tools-data-science.md#vowpal-wabbit) |
| [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | <span class='red-x'>&#10060;</span> | <span class='red-x'>&#10060;</span> |  |
| LightGBM | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> （GPU 和 MPI 支持） |  |
| H2O | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| CatBoost | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Intel MKL | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| OpenCV | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Dlib | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Docker | <span class='green-check'>&#9989;</span> <br/> （仅限 Windows 容器） | <span class='green-check'>&#9989;</span> |  |
| Nccl | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Rattle | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| ONNX 运行时 | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |



## <a name="store-retrieve-and-manipulate-data"></a>存储、检索和操作数据

| 工具 | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 使用注意事项 |
|--|-:|:-:|:-:|
| 关系数据库 | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer Edition | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer Edition | [DSVM 上的 SQL Server](./dsvm-tools-data-platforms.md#sql-server-developer-edition) |
| 数据库工具 | SQL Server Management Studio<br/> SQL Server Integration Services<br/> [bcp、sqlcmd](/sql/tools/command-prompt-utility-reference-database-engine) | [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)（查询工具） <br /> bcp、sqlcmd <br /> ODBC/JDBC 驱动程序 |  |
| [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [Azure CLI](/cli/azure) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [AzCopy](../../storage/common/storage-use-azcopy-v10.md) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> | [DSVM 上的 AzCopy](./dsvm-tools-ingestion.md#azcopy) |
| [Blob FUSE 驱动程序](https://github.com/Azure/azure-storage-fuse) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [DSVM 上的 blobfuse](./dsvm-tools-ingestion.md#blobfuse) |
| [Azure Cosmos DB 数据迁移工具](../../cosmos-db/import-data.md) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [DSVM 上的 Cosmos DB](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| Unix/Linux 命令行工具 | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Apache Spark 3.1（独立版） | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> |  |

## <a name="program-in-python-r-julia-and-nodejs"></a>使用 Python、R、Julia 和 Node.js 编程

| 工具 | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 使用注意事项 |
|--|:-:|:-:|:-:|
| 预安装了常用包的 [CRAN-R](https://cran.r-project.org/) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| 预安装了常用包的 [Anaconda Python](https://www.continuum.io/) | <span class='green-check'>&#9989;</span><br/> (Miniconda) | <span class='green-check'>&#9989;</span></br> (Miniconda) |  |
| [Julia (Julialang)](https://julialang.org/) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterHub（多用户 Notebook 服务器） | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterLab（多用户 Notebook 服务器） | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| Node.js | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| 带有以下内核的 [Jupyter Notebook Server](https://jupyter.org/)： | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span> | [Jupyter Notebook 示例](./dsvm-samples-and-walkthroughs.md) |
| &nbsp;&nbsp;&nbsp;&nbsp; R |  |  | [R Jupyter 示例](./dsvm-samples-and-walkthroughs.md#r-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Python |  |  | [Python Jupyter 示例](./dsvm-samples-and-walkthroughs.md#python-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Julia |  |  | [Julia Jupyter 示例](./dsvm-samples-and-walkthroughs.md#julia-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; PySpark |  |  | [pySpark Jupyter 示例](./dsvm-samples-and-walkthroughs.md#sparkml) |

Ubuntu 18.04 DSVM 和 Windows Server 2019 DSVM 具有以下 Jupyter 内核：</br> 
* Python 3.8 - 默认</br>  
* Python 3.8 - PyTorch</br>  
* Python 3.8 - TensorFlow</br>  
* Python 3.6 - AzureML - TensorFlow</br>  
* Python 3.6 - AzureML - PyTorch</br>  
* Python 3.6 - AzureML - AutoML</br>  
* R</br>  
* Python 3.7 - Spark（本地）</br>  
* Julia 1.2.0</br>  
* R Spark – HDInsight</br>  
* Scala Spark – HDInsight</br>  
* Python 3 Spark - HDInsight</br>  

Ubuntu 18.04 DSVM 和 Windows Server 2019 DSVM 具有以下 conda 环境：</br> 
* py38_default  </br>
* py38_tensorflow </br> 
* py38_pytorch  </br>
* azureml_py36_tensorflow  </br>
* azureml_py36_pytorch  </br>
* azureml_py36_automl  </br>


## <a name="use-your-preferred-editor-or-ide"></a>使用你喜欢的编辑器或 IDE

| 工具 | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 使用注意事项 |
|--|:-:|:-:|:-:|
| [Notepad++](https://notepad-plus-plus.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Nano](https://www.nano-editor.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Visual Studio 2019 Community Edition](https://www.visualstudio.com/community/) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [DSVM 上的 Visual Studio](dsvm-tools-development.md#visual-studio-community-edition) |
| [Visual Studio Code](https://code.visualstudio.com/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM 上的 Visual Studio Code](./dsvm-tools-development.md#visual-studio-code) |
| [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM 上的 RStudio Desktop](./dsvm-tools-development.md#rstudio-desktop) |
| [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) <br/> （默认已禁用） | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM 上的 PyCharm](./dsvm-tools-development.md#pycharm) |
| [IntelliJ IDEA](https://www.jetbrains.com/idea/) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [Vim](https://www.vim.org) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Git](https://git-scm.com/) 和 Git Bash | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [OpenJDK](https://openjdk.java.net) 11 | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| .NET Framework | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| Azure SDK | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |

## <a name="organize--present-results"></a>组织和展示结果

| 工具 | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 使用注意事项 |
|--|:-:|:-:|:-:|
| [Microsoft 365](https://www.microsoft.com/microsoft-365)（Word、Excel、PowerPoint） | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Microsoft Teams](https://www.microsoft.com/microsoft-teams) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Power BI Desktop](https://powerbi.microsoft.com/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| Microsoft Edge 浏览器 | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
