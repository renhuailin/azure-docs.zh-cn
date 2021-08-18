---
title: 机器学习和数据科学工具
titleSuffix: Azure Data Science Virtual Machine
description: 了解 Data Science Virtual Machine 上预装的机器学习工具和框架。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: 86cbac686c2f994dff4042ea2a227156d9e45472
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742046"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Azure Data Science Virtual Machine 上的机器学习和数据科学工具
Azure Data Science Virtual Machine (DSVM) 拥有丰富的用于机器学习的工具和库，这些工具和库以 Python、R 和 Julia 等热门语言提供。

以下是 DSVM 上的部分机器学习工具和库。

## <a name="azure-machine-learning-sdk-for-python"></a>适用于 Python 的 Azure 机器学习 SDK

查看[适用于 Python 的 Azure 机器学习 SDK](../overview-what-is-azure-machine-learning.md) 的完整参考。

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   |   Azure 机器学习是一款可用于开发和部署机器学习模型的云服务。 可以在使用 Python SDK 构建、训练、缩放和管理模型时跟踪模型。 将模型部署为容器，并在云中、在本地或在 Azure IoT Edge 上运行它们。   |
| 支持的版本     | Windows（conda 环境：AzureML），Linux（conda 环境：py36）    |
| 典型用途      | 常规机器学习平台      |
| 如何配置或安装它？      |  使用 GPU 支持安装   |
| 如何使用或运行它      | 作为 Python SDK 在 Azure CLI 中使用。 激活到 Windows 版本上的 conda 环境 `AzureML` 或 Linux 版本上的 `py36`。      |
| 指向示例的链接      | Notebook 下的 `AzureML` 目录中包含了示例 Jupyter Notebook。  |

## <a name="h2o"></a>H2O

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   | 一个支持内存中、分布式、快速且可缩放机器学习的开放源代码 AI 平台。  |
| 支持的版本      | Linux   |
| 典型用途      | 常规用途的分布式可缩放机器学习   |
| 如何配置或安装它？      | H2O 安装在 `/dsvm/tools/h2o` 中。      |
| 如何使用或运行它      | 使用 X2Go 连接到 VM。 启动新的终端并运行 `java -jar /dsvm/tools/h2o/current/h2o.jar`。 然后启动 Web 浏览器并连接到 `http://localhost:54321`。      |
| 指向示例的链接      | 在 `h2o` 目录的 Jupyter 中的虚拟机上提供有示例。      |

DSVM 上还有其他几个机器学习库，如适用于 DSVM 的 Anaconda Python 分发版中的常用 `scikit-learn` 包。 若要查看 Python、R 和 Julia 中可用的程序包列表，请运行相应的程序包管理器。

## <a name="lightgbm"></a>LightGBM

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   | 一个快速、分布式、高性能的梯度提升（GBDT、GBRT、GBM 或 MART）框架，基于决策树算法。 它用于排名、分类和许多其他机器学习任务。    |
| 支持的版本      | Windows、Linux    |
| 典型用途      | 常规用途的梯度提升框架      |
| 如何配置或安装它？      | 在 Windows 上，LightGBM 作为 Python 程序包安装。 在 Linux 上，命令行可执行文件位于 `/opt/LightGBM/lightgbm` 中，安装了 R 程序包，并安装了 Python 程序包。     |
| 指向示例的链接      | [LightGBM 指南](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |

## <a name="rattle"></a>Rattle
| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   |   一个用于使用 R 进行数据挖掘的图形用户界面。   |
| 支持的版本     | Windows、Linux     |
| 典型用途      | R 适用的常规 UI 数据挖掘工具    |
| 如何使用或运行它      | 作为 UI 工具使用。 在 Windows 上，启动命令提示符，运行 R，然后在 R 中运行 `rattle()`。 在 Linux 上，使用 X2Go 连接，启动终端，运行 R，然后在 R 中运行 `rattle()`。 |
| 指向示例的链接      | [Rattle](https://togaware.com/onepager/) |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   |   一个快速的开放源代码外存学习系统库    |
| 支持的版本     | Windows、Linux     |
| 典型用途      | 常规机器学习库      |
| 如何配置或安装它？      |  Windows：msi 安装程序<br/>Linux：apt-get |
| 如何使用或运行它      | 作为 on-path 命令行工具（Windows 上为 `C:\Program Files\VowpalWabbit\vw.exe`，Linux 上为 `/usr/bin/vw`）    |
| 指向示例的链接      | [VowPal Wabbit 示例](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |

## <a name="weka"></a>Weka
| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   |  适用于数据挖掘任务的机器学习算法的集合。 这些算法可以直接应用于数据集，也可以从你自己的 Java 代码中调用。 Weka 包含用于数据预处理、分类、回归、群集、关联规则和可视化的工具。 |
| 支持的版本     | Windows、Linux     |
| 典型用途      | 常规机器学习工具     |
| 如何使用或运行它      | 在 Windows 上，在“开始”菜单中搜索 Weka。 在 Linux 上，使用 X2Go 进行登录，然后转到“应用程序” > “开发” > “Weka”。   |
| 指向示例的链接      | [Weka 示例](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |

## <a name="xgboost"></a>XGBoost 
| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   |   一个快速、可移植的分布式梯度提升（GBDT、GBRT 或 GBM）库，适用于 Python、R、Java、Scala、C++ 等。 它可在单台计算机、Apache Hadoop 和 Spark 上运行。    |
| 支持的版本     | Windows、Linux     |
| 典型用途      | 常规机器学习库      |
| 如何配置或安装它？      |  使用 GPU 支持安装   |
| 如何使用或运行它      | 作为 Python 库（2.7 和 3.6+）、R 程序包，以及 on-path 命令行工具（`C:\dsvm\tools\xgboost\bin\xgboost.exe` 适用于 Windows，`/dsvm/tools/xgboost/xgboost` 适用于 Linux）    |
| 指向示例的链接      | 虚拟机上包含了示例，在 Linux 上位于 `/dsvm/tools/xgboost/demo` 中，在 Windows 上位于 `C:\dsvm\tools\xgboost\demo` 中。   |
