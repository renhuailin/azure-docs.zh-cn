---
title: 将数据以自定义日志格式收集到 Azure Sentinel | Microsoft Docs
description: 使用 Log Analytics 代理从自定义数据源收集数据并将其引入到 Azure Sentinel 中。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: d99785da00f277480505b417947781821df1caa2
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260900"
---
# <a name="collect-data-in-custom-log-formats-to-azure-sentinel-with-the-log-analytics-agent"></a>使用 Log Analytics 代理将数据以自定义日志格式收集到 Azure Sentinel

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

许多应用程序将数据记录到文本文件，而不是标准日志记录服务（如 Windows 事件日志或 Syslog）。 可以使用 Log Analytics 代理从 Windows 和 Linux 计算机的非标准格式的文本文件中收集数据。 在收集后，可以将数据分析到查询中的各个字段，或者在收集期间将数据提取到各个字段。

本文介绍如何使用自定义日志格式将数据源连接到 Azure Sentinel。 有关使用此方法的支持数据连接器的详细信息，请参阅[数据连接器参考](data-connectors-reference.md)。

若要详细了解[自定义日志](../azure-monitor/agents/data-sources-custom-logs.md)，请参阅 Azure Monitor 文档。

与 Syslog 类似，配置自定义日志收集有两个步骤：

- 在将生成日志的 Linux 或 Windows 计算机上安装 Log Analytics 代理。

- 配置应用程序的日志记录设置。

- 从 Azure Sentinel 内配置 Log Analytics 代理。

## <a name="install-the-log-analytics-agent"></a>安装 Log Analytics 代理

在将生成日志的 Linux 或 Windows 计算机上安装 Log Analytics 代理。

> [!NOTE]
> 某些供应商建议将 Log Analytics 代理安装在单独的日志服务器上，而不是直接安装在设备上。 请查看[数据连接器参考](data-connectors-reference.md)页上的产品部分或产品自己的文档。

选择下面相应的选项卡，具体取决于你的连接器在 Azure Sentinel 中是否有数据连接器页。

# <a name="from-a-specific-data-connector-page"></a>[来自特定的数据连接器页](#tab/DCG)

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 选择设备类型，然后选择“打开连接器页”。

1. 在生成日志的设备上安装并加入代理。 根据需要选择“Linux”或“Windows”。

    | 计算机类型 | Instructions |
    | --------- | --------- |
    | 对于 Azure Linux VM | <ol><li>在“选择安装 Linux 代理的位置”下，展开“在 Azure Linux 虚拟机上安装代理”。<br><br><li>选择“下载并安装用于 Azure Linux 虚拟机的代理 >”链接。<br><br><li>在“虚拟机”边栏选项卡中，选择要安装代理的虚拟机，然后选择“连接”。 对于要连接的每个 VM，重复此步骤。 |
    | 对于任何其他 Linux 计算机 | <ol><li>在“选择安装 Linux 代理的位置”下，展开“在非 Azure Linux 计算机上安装代理”。<br><br><li>选择“下载并安装用于非 Azure Linux 虚拟机的代理 >”链接。<br><br><li>在“代理管理”边栏选项卡中，选择“Linux 服务器”选项卡，然后复制用于“下载和载入适用于 Linux 的代理”的命令并在 Linux 计算机上运行。<br><br> 如果要保留 Linux 代理安装文件的本地副本，请选择“下载和载入代理”命令上方的“下载 Linux 代理”链接。|
    | 对于 Azure Windows VM | <ol><li>在“选择安装 Windows 代理的位置”下，展开“在 Azure Windows 虚拟机上安装代理”。<br><br><li>选择“下载并安装用于 Azure Windows 虚拟机的代理 >”链接。<br><br><li>在“虚拟机”边栏选项卡中，选择要安装代理的虚拟机，然后选择“连接”。 对于要连接的每个 VM，重复此步骤。 |
    | 对于任何其他 Windows 计算机 | <ol><li>在“选择安装 Windows 代理的位置”下，展开“在非 Azure Windows 计算机上安装代理”<br><br><li>选择“下载并安装用于非 Azure Windows 计算机的代理 >”链接。<br><br><li>在“代理管理”边栏选项卡的“Windows 服务器”选项卡上，根据需要为 32 位或 64 位系统选择“下载 Windows 代理”链接。 |


# <a name="other-data-sources"></a>[其他数据源](#tab/CUS)

1. 从 Azure Sentinel 导航菜单中，选择“设置”，然后选择“工作区设置”选项卡。

1. 在生成日志的设备上安装并加入代理。 根据需要选择“Linux”或“Windows”。

    | 计算机类型 | Instructions |
    | --------- | --------- |
    | Azure VM（Windows 或 Linux） | <ol><li>从 Log Analytics 工作区导航菜单中，选择“虚拟机”。<br><br><li>在“虚拟机”边栏选项卡中，选择要安装代理的虚拟机，然后选择“连接”。<br>对于要连接的每个 VM，重复此步骤。 |
    | 任何其他 Windows 或 Linux 计算机 | <ol><li>从 Log Analytics 工作区导航菜单中，选择“代理管理”。<br><br><li>根据需要选择“Windows 服务器”或“Linux 服务器”选项卡。<br><br><li>对于 Windows，根据需要为 32 位或 64 位系统选择“下载 Windows 代理”链接。 对于 Linux，复制用于“下载和载入适用于 Linux 的代理”的命令并通过命令行运行它，或选择“下载 Linux 代理”链接以下载安装文件的本地副本。 |

---

## <a name="configure-the-logs-to-be-collected"></a>配置要收集的日志

许多设备类型都有自己的数据连接器，它们显示在 Azure Sentinel 的“数据连接器”页中。 其中一些连接器需要特殊的额外说明，以便在 Azure Sentinel 中正确设置日志收集。 这些说明可能包含基于 Kusto 函数的分析程序的实现。 

在 Azure Sentinel 中列出的所有连接器都将在门户中各自的连接器页面以及 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)页面的相应部分中显示任何特定的说明。

如果产品未在“数据连接器”页中列出，请查看供应商的文档，了解有关为设备配置日志记录的说明。

## <a name="configure-the-log-analytics-agent"></a>配置 Log Analytics 代理

1. 从连接器页中，选择“打开工作区自定义日志配置”链接。
    或者，从 Log Analytics 工作区导航菜单中，选择“自定义日志”。

1. 在“自定义表”选项卡中，选择“添加自定义日志”。

1. 在“示例”选项卡上，从设备上传日志文件的示例（例如，access.log 或 error.log）。 然后，选择“下一步”  。

1. 在“记录分隔符”选项卡中，选择记录分隔符“换行符”或“时间戳”（参见有关该选项卡的说明），然后选择“下一步”。

1. 在“集合路径”选项卡中，选择路径类型“Windows”或“Linux”，并根据配置输入设备的日志路径。 然后，选择“下一步”  。

1. 为自定义日志指定名称和说明（可选），然后选择“下一步”。  
    名称不要以“_CL”结尾，因为它将自动追加。


## <a name="find-your-data"></a>查找数据

若要在“日志”中查询自定义日志数据，请在查询窗口中键入为自定义日志（以“_CL”结尾）指定的名称。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何收集自定义日志类型中的数据以引入到 Azure Sentinel 中。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。
