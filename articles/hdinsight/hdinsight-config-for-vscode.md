---
title: Azure HDInsight 配置设置参考
description: 介绍 Azure HDInsight 扩展的配置。
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: e5bfa66f7c7d22617c17c6bd57eff373574021f4
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108142056"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Azure HDInsight 配置设置参考

适用于 Visual Studio Code 的 Spark 和 Hive 工具扩展是高度可配置的。 本页介绍你可以使用的重要设置。  

有关使用 VS Code 中的设置的常规信息，请参阅[用户和工作区设置](https://code.visualstudio.com/docs/getstarted/settings)以及[变量参考](https://code.visualstudio.com/docs/editor/variables-reference)，了解有关预定义变量支持的信息。

## <a name="open-the-azure-hdinsight-configuration"></a>打开 Azure HDInsight 配置

1. 首先打开一个文件夹以创建工作区设置。
2. 按 Ctrl + Shift + P，或导航到“视图” -> “命令面板...”以显示所有命令。
3. 搜索“设置配置”。
4. 展开左侧目录中的“扩展”，然后选择“HDInsight 配置” 。

 ![hdi 配置图像](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>常规设置   

|  属性   | 默认 | 说明   |
| ----- | ----- |----- |
| HDInsight：Azure 环境 | Azure | Azure 环境 |
| HDInsight：禁用开启调查链接 | 已选中 | 启用/禁用开启 HDInsight 调查 |
| HDInsight：启用跳过 Pyspark 安装 | 未选中 | 启用/禁用跳过 pyspark 安装 |
| HDInsight：登录提示启用 | 未选中 | 选中此选项后，登录 Azure 时会出现提示 |
| HDInsight：上一个扩展版本 | 显示当前扩展的版本号 | 显示上一个扩展版本|
| HDInsight：结果字体系列 | -apple-system,BlinkMacSystemFont,Segoe WPC,Segoe UI,HelveticaNeue-Light,Ubuntu,Droid Sans,sans-serif | 设置结果网格的字体系列；留空时使用编辑器字体 |
| HDInsight：结果字号 | 13 |设置结果网格的字号；留空时使用编辑器字体 |
| HDInsight 群集：链接的群集 | -- | 链接的群集 url。 也可以编辑要设置的 JSON 文件 |
| HDInsight Hive：应用本地化 | 未选中 | [可选] 本选项控制是否使用 VSCode 的区域设置进行本地化（必须重启 VSCode，设置才能生效）|
| HDInsight Hive：复制包含标题 | 未选中 | [可选] 用于从结果视图复制结果的配置选项 |
| HDInsight Hive：复制删除新行 | 已选中 | [可选] 用于从结果视图复制多行结果的配置选项 |
| HDInsight Hive › 格式：在列中对齐列定义 | 未选中 | 列定义是否应对齐 |
| HDInsight Hive › 格式：数据类型大小写 | 无 | 数据类型应格式化为大写、小写还是无（不格式化） |
| HDInsight Hive › 格式：关键字大小写 | 无 | 关键字应格式化为大写、小写还是无（不格式化） |
| HDInsight Hive › 格式：在下一条语句前放置逗号 | 未选中 | 是否应将逗号置于列表中每个语句的开头（例如“, mycolumn2”）而不是结尾（例如“mycolumn1,”）|
| HDInsight Hive › 格式：将 Select 语句引用放置在新行上 | 未选中 | select 语句中的对象引用是否应拆分到单独行中？ 例如，对于“SELECT C1, C2 FROM T1”，C1 和 C2 将位于单独行上
| HDInsight Hive：日志调试信息 | 未选中 | [可选] 将调试输出记录到 VS Code 控制台（“帮助”->“切换开发人员工具”） 
| HDInsight Hive：消息默认打开 | 已选中 | True 时默认开启消息面板；false 时默认关闭|
| HDInsight Hive：结果字体系列 | -apple-system,BlinkMacSystemFont,Segoe WPC,Segoe UI,HelveticaNeue-Light,Ubuntu,Droid Sans,sans-serif | 设置结果网格的字体系列；留空时使用编辑器字体 |
| HDInsight Hive：结果字号 | 13 | 设置结果网格的字号；留空时使用编辑器字体 |
| HDInsight Hive › 另存为 Csv：包含标题 | 已选中 | [可选] 为 true 时，将保存结果为 CSV 时包含列标题 |
| HDInsight Hive：快捷方式 | -- | 与“结果”窗口相关的快捷方式 |
| HDInsight Hive：显示批处理时间| 未选中 | [可选] 是否显示每个批处理的执行时间 |
| HDInsight Hive：拆分窗格选择 | 下一步 | [可选] 用于指示新结果窗格应在哪一列打开的配置选项 |
| HDInsight 作业提交：群集配置 | -- | 群集配置 |
| HDInsight 作业提交：Livy 配置 | -- | Livy 配置。 POST/批处理 |
| HDInsight Jupyter：追加结果| 已选中 | 是否将结果追加到结果窗口，否则会清除并显示。 |
| HDInsight Jupyter：语言 | -- | 每种语言的默认设置。 |
| HDInsight Jupyter › 日志：详细 | 未选中 | 是否启用详细日志记录 |
| HDInsight Jupyter › Notebook：启动参数 | 可以添加项 | “jupyter notebook”命令行参数。 每个参数都是数组中的单独项。 要获取完整列表，请在终端窗口中键入“jupyter notebook--help”。 |
| HDInsight Jupyter › Notebook：启动文件夹 | ${workspaceRoot} |-- |
| HDInsight Jupyter：Python 扩展已启用 | 已选中 | 提交 pySpark 交互作业时使用 ms-python 扩展的 Python 交互窗口。 否则，使用我们自己的 jupyter 窗口 |
| HDInsight Spark.NET：7z | C:\Program Files\7-Zip | <7z.exe 的路径> |
| HDInsight Spark.NET：HADOOP_HOME | D:\winutils | <bin\winutils.exe 的路径> 仅限 Windows OS |
| HDInsight Spark.NET：JAVA_HOME | C:\Program Files\Java\jdk1.8.0_201\ | Java 主文件夹的路径|
| HDInsight Spark.NET：SCALA_HOME | C:\Program Files (x86)\scala\ | Scala 主文件夹的路径 |
| HDInsight Spark.NET：SPARK_HOME | D:\spark-2.3.3-bin-hadoop2.7\ | Spark 主文件夹的路径 |
| Hive：保持查询结果选项卡 | 未选中 | Hive PersistQueryResultTabs |
| Hive：拆分窗格选择 | 下一步 | [可选] 用于指示新结果窗格应在哪一列打开的配置选项 |
| Hive 交互：复制可执行文件夹 | 未选中 | 是否将 hive 交互服务运行时文件夹复制到用户的 tmp 文件夹 |
| Hql 交互服务器：包装器端口 | 13424 | Hive 交互服务端口 |
| Hql 语言服务器：语言包装器端口 | 12342 | 服务器侦听的 Hive 语言服务端口。 |
| Hql 语言服务器：最大问题数 | 100 | 控制服务器生成的问题的最大数量。 |
| Synapse Spark 计算：Synapse Spark 计算 Azure 环境 | blank | synapse Spark 计算 Azure 环境 |
| Synapse Spark 池作业提交：Livy 配置 | -- | Livy 配置。 POST/批处理
| Synapse Spark 池作业提交：Synapse Spark 池群集配置 | -- | Synapse Spark 池配置 |


## <a name="next-steps"></a>后续步骤

- 有关适用于 VSCode 的 Azure HDInsight 的信息，请参阅[适用于 Visual Studio Code 的 Spark 和 Hive 工具](/sql/big-data-cluster/spark-hive-tools-vscode)。
- 有关演示了如何使用适用于 Visual Studio Code 的 Spark 和 Hive 的视频，请观看[适用于 Visual Studio Code 的 Spark 和 Hive](https://go.microsoft.com/fwlink/?linkid=858706)。