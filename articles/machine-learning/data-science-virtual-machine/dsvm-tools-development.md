---
title: 开发工具
titleSuffix: Azure Data Science Virtual Machine
description: 了解 Data Science Virtual Machine 上提供的工具和集成开发环境。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: efe57637347b5886827f5da443acd9240974d687
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070959"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine 上的开发工具

Data Science Virtual Machine (DSVM) 在高效集成开发环境 (IDE) 中捆绑了几个常用工具。 以下是 DSVM 中提供的一些工具。

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

| 类别 | 值 |
|--|--|
| 它是什么？ | 常规用途 IDE |
| 支持的 DSVM 版本 | Windows Server 2019：Visual Studio 2019 |
| 典型用途 | 软件开发 |
| 如何在 DSVM 上配置和安装它？ | 数据科学工作负荷（Python 和 R 工具）、Azure 工作负荷（Hadoop、Data Lake）、Node.js、SQL Server 工具、[用于 Visual Studio Code 的 Azure 机器学习](https://github.com/Microsoft/vs-tools-for-ai) |
| 如何使用和运行它 | 桌面快捷方式 (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`)。 通过图表，使用桌面图标或“开始”菜单打开 Visual Studio。 依次搜索程序（按 Windows 徽标键+S）、**Visual Studio**。 然后，可以使用 C#、Python、R 和 Node.js 等语言来创建项目。 |

> [!NOTE]
> 可能会有一条消息指出评估期已过。 在此情况下，请输入 Microsoft 帐户凭据。 或者新建一个免费帐户来获取 Visual Studio Community 的访问权限。

## <a name="visual-studio-code"></a>Visual Studio Code 

| 类别 | 值 |
|--|--|
| 它是什么？ | 常规用途 IDE |
| 支持的 DSVM 版本 | Windows、Linux |
| 典型用途 | 代码编辑器和 Git 集成 |
| 如何使用和运行它 | Windows 上的桌面快捷方式 (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`)、Linux 上的桌面快捷方式或终端 (`code`) |

## <a name="rstudio-desktop"></a>RStudio Desktop

| 类别 | 值 |
|--|--|
| 它是什么？ | R 语言的客户端 IDE |
| 支持的 DSVM 版本 | Windows、Linux |
| 典型用途 | R 开发 |
| 如何使用和运行它 | Windows 上的桌面快捷方式 (`C:\Program Files\RStudio\bin\rstudio.exe`)，Linux 上的桌面快捷方式 (`/usr/bin/rstudio`) |

## <a name="pycharm"></a>PyCharm

| 类别 | 值 |
|--|--|
| 它是什么？ | Python 语言的客户端 IDE |
| 支持的 DSVM 版本 | Windows 2019、Ubuntu 18.04 |
| 典型用途 | Python 开发 |
| 如何使用和运行它 | Windows 上的桌面快捷方式 (`C:\Program Files\tk`)。 Linux 上的桌面快捷方式 (`/usr/bin/pycharm`) |
