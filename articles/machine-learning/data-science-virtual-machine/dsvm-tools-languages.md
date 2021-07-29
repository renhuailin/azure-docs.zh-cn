---
title: 支持的语言
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine 上预安装的支持的程序语言和相关工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: fcb2a4d9860687fb18409666f81839002329d54e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070891"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>数据科学虚拟机支持的语言 

Data Science Virtual Machine (DSVM) 附带了几种预构建的语言和开发工具，用于构建人工智能 (AI) 应用程序。 以下是一些值得注意的语言和开发工具。

## <a name="python"></a>Python

| 类别 | 值 |
|--|--|
| 支持的语言版本 | Python 3.8 |
| 支持的 DSVM 版本 | Windows Server 2019，Ubuntu 18.04 |
| 如何在 DSVM 上配置/安装它？ | 有多个 `conda` 环境，其中每个环境都预安装了不同的 Python 包。 若要列出计算机中所有可用的环境，请运行 `conda env list`。 |

### <a name="how-to-use-and-run-it"></a>如何使用和运行它

* 在命令提示符下运行：

  打开命令提示符，并根据要运行的 Python 版本使用以下方法之一：

    ```
    conda activate <conda_environment_name>
    python --version
    ```
    
* 在 IDE 中使用：

  DSVM 映像安装了多个 IDE，例如 VS.Code 或 PyCharm。 可以使用它们来编辑、运行和调试 Python 脚本。

* 在 Jupyter 实验室中使用：

  在 Jupyter 实验室中打开“Launcher”选项卡，然后选择新文档的类型和内核。 如果想要将文档放在特定文件夹中，请首先在左侧的“文件浏览器”中导航到该文件夹。

* 安装 Python 包：

  若要安装新包，首先需要激活正确的环境。 环境是安装新包的位置，之后该包将仅在该环境中可用。

  若要激活环境，请运行 `conda activate <environment_name>`。 环境激活后，可以使用包管理器（如 `conda` 或 `pip`）来安装或更新包。

  或者，如果使用的是 Jupyter，则还可以直接在单元中运行 `!pip install --upgrade <package_name>` 来安装包。

## <a name="r"></a>R

| 类别 | 值 |
|--|--|
| 支持的语言版本 | CRAN R 4.0.5 |
| 支持的 DSVM 版本 | Linux、Windows |

### <a name="how-to-use-and-run-it"></a>如何使用和运行它

* 在命令提示符下运行：

  打开命令提示符并键入 `R`。

* 在 IDE 中使用：

  若要在 IDE 中编辑 R 脚本，可以使用默认安装在 DSVM 映像上的 RStudio。

* 在 Jupyter 实验室中使用

  在 Jupyter 实验室中打开“Launcher”选项卡，然后选择新文档的类型和内核。 如果想要将文档放在特定文件夹中，请首先在左侧的“文件浏览器”中导航到该文件夹。

* 安装 R 包：

  可以使用 `install.packages()` 函数或 RStudio 安装新 R 包。

## <a name="julia"></a>Julia

| 类别 | 值 |
| ------------- | ------------- |
| 支持的语言版本 | 1.0.5 |
| 支持的 DSVM 版本      | Linux、Windows     |


### <a name="how-to-use-and-run-it"></a>如何使用和运行它    

* 在命令提示符下运行

  打开命令提示符并运行 `julia`。

* 在 Jupyter 中使用：

  在 Jupyter 中打开“Launcher”选项卡，然后选择新文档的类型和内核。 如果想要将文档放在特定文件夹中，请首先在左侧的“文件浏览器”中导航到该文件夹。

* 安装 Julia 包：

  可以使用 Julia 包管理器命令（如 `Pkg.add()`）来安装或更新包。


## <a name="other-languages"></a>其他语言

**C#** ：在 Windows 上可用，可通过 Visual Studio Community Edition 访问，或在 `Developer Command Prompt for Visual Studio` 处运行 `csc` 命令以进行访问。

**Java**：OpenJDK 在 Linux 和 Windows 版本的 DSVM 上均可使用，并在路径上进行设置。 若要使用 Java，请在 Windows 中的命令提示符处或在 Linux 中的 bash shell 上键入 `javac` 或 `java` 命令。

**Node.js**：Node.js 在 Linux 和 Windows 版本的 DSVM 上均可使用，并在路径上进行设置。 若要访问 Node.js，请在 Windows 中的命令提示符处或在 Linux 中的 bash shell 上键入 `node` 或 `npm` 命令。 在 Windows 上，安装了 Node.js 工具的 Visual Studio 扩展，以提供图形化 IDE 来开发 Node.js 应用程序。

**F#** ：在 Windows 上可用，并可通过 Visual Studio Community Edition 访问，或在 `Developer Command Prompt for Visual Studio` 上运行 `fsc` 命令即可进行访问。
