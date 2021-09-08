---
title: 将 Syslog 数据连接到 Azure Sentinel | Microsoft Docs
description: 在 Linux 计算机上在设备和 Azure Sentinel 之间使用代理可将支持 Syslog 的任何计算机或设备连接到 Azure Sentinel。
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
ms.date: 08/15/2021
ms.author: yelevin
ms.openlocfilehash: 807083fa77023753382abb96419c9cd5fe689b33
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254272"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>使用 Syslog 从基于 Linux 的源收集数据

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Syslog 是普遍适用于 Linux 的事件日志记录协议。 可以使用内置于 Linux 设备的 Syslog 守护程序来收集指定类型的本地事件，并让它使用适用于 Linux 的 Log Analytics 代理（以前称为 OMS 代理）将这些事件发送到 Azure Sentinel。

本文介绍如何使用 Syslog 将数据源连接到 Azure Sentinel。 有关此方法支持的连接器的详细信息，请参阅[数据连接器参考](data-connectors-reference.md)。

## <a name="architecture"></a>体系结构

如果在 VM 或设备上安装了 Log Analytics 代理，则安装脚本会将本地 Syslog 守护程序配置为将消息转发到 UDP 端口 25224 上的代理。 接收到消息后，代理通过 HTTPS 将它们发送到 Log Analytics 工作区，在该工作区中，它们被引入到“Azure Sentinel”>“Logs”中的 Syslog 表。

有关详细信息，请参阅 [Azure Monitor 中的 Syslog 数据源](../azure-monitor/agents/data-sources-syslog.md)。

:::image type="content" source="media/connect-syslog/syslog-diagram.png" alt-text="此示意图显示从 syslog 源到 Azure Sentinel 工作区的数据流，其中 Log Analytics 代理直接安装在数据源设备上。":::

对于一些不允许在本地安装 Log Analytics 代理的设备类型，可以改为在基于 Linux 的专用日志转发器上安装代理。 必须将发起设备配置为将 Syslog 事件发送到此转发器上的 Syslog 守护程序，而不是本地守护程序。 转发器上的 Syslog 守护程序通过 UDP 将事件发送到 Log Analytics 代理。 如果此 Linux 转发器需要收集大量 Syslog 事件，则其 Syslog 守护程序会通过 TCP 向代理发送事件。 在任一情况下，代理随后会将这些事件从那里发送到 Azure Sentinel 中的 Log Analytics 工作区。

:::image type="content" source="media/connect-syslog/syslog-forwarder-diagram.png" alt-text="此示意图显示从 syslog 源到 Azure Sentinel 工作区的数据流，其中 Log Analytics 代理安装在单独的日志转发设备上。":::

> [!NOTE]
> - 如果你的设备支持通过 Syslog 的通用事件格式 (CEF)，则会收集更完整的数据集，并在收集时对数据进行分析。 你应选择此选项，并按照[从设备将 CEF 格式的日志获取到 Azure Sentinel](connect-common-event-format.md) 中的说明进行操作。
>
> - Log Analytics 支持由 rsyslog 或 syslog-ng 守护程序（其中 rsyslog 为默认守护程序）发送的消息集合。  不支持将 Red Hat Enterprise Linux (RHEL) 版本 5、CentOS 和 Oracle Linux 版本上的默认 syslog 守护程序 (sysklog) 用于 syslog 事件收集。 要从这些发行版的此版本中收集 syslog 数据，应安装并配置 rsyslog 守护程序以替换 sysklog。

配置 Syslog 集合有三个步骤：

- 配置 Linux 设备。 这是指将安装 Log Analytics 代理的设备，无论它是产生事件的同一设备还是转发事件的日志收集器。

- 对应于将事件发送到代理的 Syslog 守护程序的位置，配置应用程序的日志记录设置。

- 配置 Log Analytics 代理本身。 此操作在 Azure Sentinel 中完成，并且配置被发送到所有已安装的代理。

## <a name="configure-your-linux-machine-or-appliance"></a>配置 Linux 计算机或设备

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从连接器库中，选择“Syslog”，然后选择“打开连接器”页面 。

    如果设备类型列在 Azure Sentinel 数据连接器库中，请选择设备的连接器，而不是通用 Syslog 连接器。 如果设备类型有额外或特殊说明，你将在设备的连接器页上看到这些说明以及自定义内容，如工作簿和分析规则模板。

1. 安装 Linux 代理。 在“选择代理安装位置：”下

    |计算机类型  |Instructions  |
    |---------|---------|
    |对于 Azure Linux VM     |    1. 展开“在 Azure Linux 虚拟机上安装代理”。 <br><br>2. 选择“下载并安装用于 Azure Linux 虚拟机的代理 >”链接。<br><br>3. 在“虚拟机”边栏选项卡中，选择要安装代理的虚拟机，然后选择“连接” 。 对于要连接的每个 VM，重复此步骤。     |
    |对于任何其他 Linux 计算机     |     1. 展开“在非 Azure Linux 计算机上安装代理” <br><br>2. 选择“下载并安装用于非 Azure Linux 虚拟机的代理 >”链接。<br><br>3. 在“代理管理”边栏选项卡中，选择“Linux 服务器”选项卡，然后复制用于“下载和载入适用于 Linux 的代理”的命令并在 Linux 计算机上运行  。<br><br>        如果要保留 Linux 代理安装文件的本地副本，请选择“下载和载入代理”命令上方的“下载 Linux 代理”链接。 |
    |     |         |

   > [!NOTE]
   > 请确保根据组织的安全策略配置这些设备的安全性设置。 例如，可以配置网络设置使其符合组织的网络安全策略，并更改守护程序中的端口和协议，使其符合安全要求。

### <a name="using-the-same-machine-to-forward-both-plain-syslog-and-cef-messages"></a>使用同一台计算机转发纯 Syslog 和 CEF 消息

也可以使用现有的 [CEF 日志转发器计算机](connect-log-forwarder.md)从普通 Syslog 源收集和转发日志。 但是，必须执行以下步骤以避免以这两种格式将事件发送到 Azure Sentinel，因为这将导致事件重复。

已根据 [CEF 源设置了数据收集](connect-common-event-format.md)，并已配置 Log Analytics 代理：

1. 在以 CEF 格式发送日志的每台计算机上，必须编辑 Syslog 配置文件，以删除用于发送 CEF 消息的设施。 这样一来，在 CEF 中发送的设施也不会在 Syslog 中发送。 有关如何执行此操作的详细说明，请参阅[在 Linux 代理上配置 Syslog](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent)。

1. 必须在这些计算机上运行以下命令，才能在 Azure Sentinel 中通过 Syslog 配置禁用代理的同步。 这可确保不会覆盖你在上一步中所做的配置更改。

    ```c
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
    ```

## <a name="configure-your-devices-logging-settings"></a>配置设备的日志记录设置

许多设备类型都有自己的数据连接器，它们显示在“数据连接器”库中。 其中一些连接器需要特殊的额外说明，以便在 Azure Sentinel 中正确设置日志收集。 这些说明可能包含基于 Kusto 函数的分析程序的实现。

在库中列出的所有连接器都将在门户中各自的连接器页面以及[ Azure Sentinel 数据连接器参考](data-connectors-reference.md)页面的相应部分中显示任何特定的说明。


## <a name="configure-the-log-analytics-agent"></a>配置 Log Analytics 代理

1. 在 Syslog 连接器边栏选项卡底部，选择“打开工作区代理配置 >”链接。

1. 在“代理配置”边栏选项卡上，选择 Syslog 选项卡。然后，添加要让连接器收集的设施。  选择“添加设施”，然后从设施下拉列表中选择。

    - 添加 syslog 设备包括在其日志标头中的设施。

    - 如果要将异常 SSH 登录检测与收集的数据一起使用，请添加 auth 和 authpriv。  有关其他详细信息，请参阅[以下部分](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)。

1. 如果已添加要监视的所有设施，请清除不想收集的任何严重级别的复选框。 默认情况下，它们都被勾选。

1. 选择“应用”。

1. 在 VM 或设备上，请确保正在发送指定的设施。

## <a name="find-your-data"></a>查找数据

1. 要在“日志”中查询 syslog 日志数据，请在查询窗口中键入 `Syslog`。

1. 可以使用[在 Azure Monitor 日志查询中使用函数](../azure-monitor/logs/functions.md)中所述的查询参数来分析 Syslog 消息。 然后，可以将查询另存为新的 Log Analytics 函数，并将其用作新的数据类型。

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>为异常 SSH 登录检测配置 Syslog 连接器

> [!IMPORTANT]
> 异常 SSH 登录检测目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

Azure Sentinel 可以将机器学习 (ML) 应用到 syslog 数据，以确定安全外壳 (SSH) 登录活动的异常情况。 方案包括：

- 不可能旅行 – 当两个成功登录事件发生在两个位置，而这两个位置无法在两个登录事件的时间范围内到达时。

- 意外位置 – 成功登录事件发生的位置可疑。 例如，最近未出现此位置。
 
此检测需要 Syslog 数据连接器的特定配置： 

1. 对于上面的[配置 Log Analytics 代理](#configure-the-log-analytics-agent)下的步骤 2，请确保选择 auth 和 authpriv 作为要监视的设施，并选择所有严重性。  

2. 留出足够的时间来收集 syslog 信息。 然后，导航到“Azure Sentinel - 日志”，复制并粘贴以下查询：
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    根据需要更改“时间范围”，并选择“运行”。 
    
    如果生成的计数为零，请确认连接器的配置，并且被监视的计算机在你为查询指定的时间段内有成功的登录活动。
    
    如果生成的计数大于零，则 syslog 数据适用于异常 SSH 登录检测。 可以通过“分析” >  “规则模板” > “（预览版）异常 SSH 登录检测”来启用此检测。  

## <a name="next-steps"></a>后续步骤
在本文中，你已了解如何将 Syslog 本地设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。
