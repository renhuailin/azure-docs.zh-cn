---
title: Azure 实验室服务中的示例类类型 | Microsoft Docs
description: 提供可以使用 Azure 实验室服务为其设置实验室的某些类型的类。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 81b3426446894b9dd7bfccc942e04c3d9a7ea492
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108278751"
---
# <a name="class-types-overview---azure-lab-services"></a>类类型概述 - Azure 实验室服务

使用 Azure 实验室服务可以在云中快速设置课堂实验室环境。 本部分列出的文章提供了有关如何使用 Azure 实验室服务设置多种类型的实验室的指导。

## <a name="adobe-creative-cloud"></a>Adobe Creative Cloud
[Adobe Creative Cloud](https://www.adobe.com/creativecloud.html) 系列的应用程序通常用于数字艺术和媒体课程。  

有关如何设置此类实验室的详细信息，请参阅[为 Adobe Creative Cloud 设置实验室](class-type-adobe-creative-cloud.md)。

## <a name="arcgis"></a>ArcGIS
[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) 是地理信息系统 (GIS) 的一个类型。  你可以设置一个使用 ArcGIS Desktop 的各种应用程序（如 [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm)）创建、编辑和分析 2D 地图的实验室。

有关如何设置此类实验室的详细信息，请参阅[设置 ArcMap\ArcGIS Desktop 实验室](class-type-arcgis.md)。

## <a name="autodesk"></a>Autodesk
[AutoDesk](https://www.autodesk.com/) 提供有关体系结构、工程、建设、设计、制造等方面的软件解决方案。  这些解决方案通常用于工程课程和[项目导向](class-type-pltw.md)课程。

有关如何设置此类实验室的详细信息，请参阅 [Autodesk](class-type-autodesk.md)。

## <a name="big-data-analytics"></a>大数据分析
你可以设置 GPU 实验室以教授大数据分析课程。 通过此类课程，学生可以了解如何处理大量数据，并应用计算机和统计学习算法来获取数据见解。 学生的主要目标是了解如何使用数据分析工具，例如 Apache Hadoop 的开源软件包，该软件包提供用于存储、管理和处理大数据的工具。 

有关如何设置此类实验室的详细信息，请参阅[设置使用 HortonWorks 数据平台的 Docker 部署进行大数据分析的实验室](class-type-big-data-analytics.md)。

## <a name="database-management"></a>数据库管理
数据库概念是大学的大多数计算机系讲授的入门课程之一。 可以在 Azure 实验室服务中为基本数据库管理类设置实验室。 例如，可以通过 [MySQL](https://www.mysql.com/) 数据库服务器或 [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) 服务器在实验室中设置一个虚拟机模板。

有关如何设置此类实验室的详细信息，请参阅[设置实验室来讲授关系数据库的数据库管理](class-type-database-management.md)。

## <a name="deep-learning-in-natural-language-processing"></a>自然语言处理中的深度学习
可以使用 Azure 实验室服务来设置一个专注于自然语言处理 (NLP) 中的深度学习的实验室。 自然语言处理 (NLP) 是某种形式的人工智能 (AI)，可在计算机中实现翻译、语音识别和其他语言理解功能。 使用 NLP 类的学生可以通过 Linux 虚拟机 (VM) 了解如何应用神经网络算法，以开发深度学习模型用于分析人类手写语言。

有关如何设置此类实验室的详细信息，请参阅[使用 Azure 实验室服务设置专注于自然语言处理中的深度学习的实验室](class-type-deep-learning-natural-language-processing.md)。

## <a name="ethical-hacking"></a>道德黑客攻击
可以为专注于道德黑客取证方面的课程设置实验室。 渗透测试是道德黑客社区使用的一种做法，当某人试图获得对系统或网络的访问权限以证明恶意攻击者可能利用的漏洞时，就会进行渗透测试。

在道德黑客课程中，学生可以学习抵御漏洞的新式技术。 每个学生都获得一个 Windows Server 主机虚拟机，它包含两个嵌套虚拟机 - 一个是带有 [Metasploitable3](https://github.com/rapid7/metasploitable3) 映像的虚拟机，另一个是带有 [Kali Linux](https://www.kali.org/) 映像的虚拟机。 Metasploitable 虚拟机用于开发目的。  Kali Linux 虚拟机用于访问执行取证任务所需的工具。

有关如何设置此类实验室的详细信息，请参阅[设置实验室以教授道德黑客课程](class-type-ethical-hacking.md)。

## <a name="matlab"></a>MATLAB
[MATLAB](https://www.mathworks.com/products/matlab.html)（代表矩阵实验室）是 [MathWorks](https://www.mathworks.com/) 的编程平台。  它结合了计算能力和可视化效果，是数学、工程、物理和化学领域的常用工具。

有关如何设置此类实验室的详细信息，请参阅[设置实验室以教授 MATLAB](class-type-matlab.md)。

## <a name="networking-with-gns3"></a>GNS3 的网络
你可以设置实验室以教授这样的课程：着重于使学生能够使用 [GNS3](https://www.gns3.com/) 软件来模拟、配置、测试虚拟和真实的网络并排查其中的问题。 

有关如何设置此类实验室的详细信息，请参阅[设置实验室以教授网络课程](class-type-networking-gns3.md)。

## <a name="project-lead-the-way-pltw"></a>项目导向 (PLTW)
[项目导向 (PLTW)](https://www.pltw.org/) 是一个非赢利组织，在美国提供计算机科学、工程和生物医学科学领域的 PreK-12 课程。  在每节 PLTW 课中，学生使用各种软件应用程序作为其实践学习体验的一部分。

有关如何设置这些类型的实验室的详细信息，请参阅[设置“项目导向”课程实验室](class-type-pltw.md)。

## <a name="python-and-jupyter-notebooks"></a>Python 和 Jupyter Notebooks
可以使用向学生讲授 [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) 使用方法所需的工具，在 Azure 实验室服务中设置模板计算机。 Jupyter Notebooks 是一个开源项目，可让你轻松地在名为“笔记本”的单张画布上组合使用丰富的文本和可执行的 [Python](https://www.python.org/) 源代码。 运行笔记本会生成输入和输出的线性记录。  这些输出可能包括文本、信息表、散点图等。

有关如何设置此类实验室的详细信息，请参阅[设置实验室以使用 Python 和 Jupyter Notebooks 讲授数据科学](class-type-jupyter-notebook.md)。

## <a name="shell-scripting-on-linux"></a>Linux 上的 Shell 脚本
可以设置一个实验室来讲解 Linux 上的 shell 脚本编写。 脚本编写是系统管理的有用组成部分，可让管理员避免重复性的任务。 在此示例场景中，类涵盖了传统的 bash 脚本和增强的脚本。 增强的脚本是结合了 bash 命令和 Ruby 的脚本。 这样，Ruby 便可以传递数据和 bash 命令来与 shell 交互。

使用这些脚本类的学生可以通过 Linux 虚拟机了解 Linux 的基础知识，并熟悉 bash shell 脚本。 该 Linux 虚拟机已启用远程桌面访问，并装有 [gedit](https://help.gnome.org/users/gedit/stable/) 和 [Visual Studio Code](https://code.visualstudio.com/) 文本编辑器。

有关如何设置此类实验室的详细信息，请参阅 [Linux 上的 Shell 脚本](class-type-shell-scripting-linux.md)。

## <a name="solidworks-computer-aided-design-cad"></a>SolidWorks 计算机辅助设计 (CAD)
你可以设置一个 GPU 实验室，使工程学生可以访问 [SolidWorks](https://www.solidworks.com/)。  SolidWorks 提供用于对固体物体进行建模的 3D CAD 环境。  借助 SolidWorks，工程师可以轻松地创建、直观显示、模拟和记录自己的设计。

有关如何设置此类实验室的详细信息，请参阅[设置使用 SolidWorks 的工程课程实验室](class-type-solidworks.md)。

## <a name="sql-database-and-management"></a>SQL 数据库和管理
结构化查询语言 (SQL) 是一种用于关系数据库管理（包括在数据库中添加、访问和管理内容）的标准语言。  你可以设置一个实验室来通过 [MySQL](https://www.mysql.com/) 数据库服务器和 [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) 服务器教授数据库概念。

有关如何设置此类实验室的详细信息，请参阅[设置实验室来讲授关系数据库的数据库管理](class-type-database-management.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [使用 Azure 实验室服务设置专注于自然语言处理中的深度学习的实验室](class-type-deep-learning-natural-language-processing.md)
- [Linux 上的 Shell 脚本](class-type-shell-scripting-linux.md)
- [道德黑客攻击](class-type-ethical-hacking.md)