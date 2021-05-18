---
title: 使用 Azure HDInsight 工具的 PySpark 交互式环境
description: 了解如何使用用于 Visual Studio Code 的 Azure HDInsight 工具来创建、提交查询和脚本。
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,交互式 Hive,交互式查询
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: a6247116cdf579691e48883231f57712da36c4ad
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864561"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>为 Visual Studio Code 设置 PySpark 交互式环境

以下步骤显示如何在 VSCode 中设置 PySpark 交互环境。 此步骤仅适用于非 Windows 用户。

我们使用 python/pip 命令在 Home 路径中生成虚拟环境。 如果要使用其他版本，则需要手动更改默认版的 python/pip 命令。 有关详细信息，请参阅[更新替代项](https://linux.die.net/man/8/update-alternatives)。

1. 安装 [Python](https://www.python.org/downloads/) 和 [pip](https://pip.pypa.io/en/stable/installing/)。

   * 从 [https://www.python.org/downloads/](https://www.python.org/downloads/) 安装 Python。 
   * 从 [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) 安装 pip（如果未从“Python 安装”进行安装）。
   * （可选）使用 `python --version` 和 `pip --version` 命令来验证是否成功安装了 Python 和 pip。 

     > [!NOTE]
     > 建议手动而不是使用 macOS 默认版本安装 Python。

2. 通过运行以下命令，安装 virtualenv。

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>其他包

在 Linux 上，如果遇到下面的错误消息，请通过运行以下两个命令来安装所需的包。

   :::image type="content" source="./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png" alt-text="安装适用于 Python 的 libkrb5 包" border="true":::

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

重启 VSCode，然后返回到 VSCode 编辑器并运行“Spark:PySPark Interactive”命令。

## <a name="next-steps"></a>后续步骤

### <a name="demo"></a>演示

* 用于 VS Code 的 HDInsight：[视频](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和扩展

* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](hdinsight-for-vscode.md)
* [使用 Azure Toolkit for IntelliJ 创建和提交 Apache Spark Scala 应用程序](spark/apache-spark-intellij-tool-plugin.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](spark/apache-spark-jupyter-notebook-install-locally.md)
