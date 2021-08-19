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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: d0f4f2ff4fdd2d33e1ac1b56b02fb185203aee39
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252588"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>使用 Syslog 从基于 Linux 的源收集数据

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

可以使用适用于 Linux 的 Log Analytics 代理（以前称为 OMS 代理），将基于 Linux 的、支持 Syslog 的计算机或设备中的事件流式传输到 Azure Sentinel。 可以对任何允许你直接在其上安装 Log Analytics 代理的计算机执行此操作。 计算机的本机 Syslog 守护程序将收集指定类型的本地事件，并在本地将其转发到代理，代理将它们流式传输到 Log Analytics 工作区。

> [!NOTE]
> - 如果你的设备支持通过 Syslog 的通用事件格式 (CEF)，则会收集更完整的数据集，并在收集时对数据进行分析。 应选择此选项，并按照[使用 CEF 连接外部解决方案](connect-common-event-format.md)中的说明进行操作。
>
> - Log Analytics 支持由 rsyslog 或 syslog-ng 守护程序（其中 rsyslog 为默认守护程序）发送的消息集合。  不支持将 Red Hat Enterprise Linux (RHEL) 版本 5、CentOS 和 Oracle Linux 版本上的默认 syslog 守护程序 (sysklog) 用于 syslog 事件收集。 要从这些发行版的此版本中收集 syslog 数据，应安装并配置 rsyslog 守护程序以替换 sysklog。

## <a name="how-it-works"></a>工作原理

Syslog 是普遍适用于 Linux 的事件日志记录协议。 如果在 VM 或设备上安装了适用于 Linux 的 Log Analytics 代理，则安装例程会将本地 Syslog 守护程序配置为将消息转发到 TCP 端口 25224 上的代理。 然后，代理通过 HTTPS 将消息发送到 Log Analytics 工作区，在该工作区中，会将消息分析为“Azure Sentinel”>“日志”中 Syslog 表中的事件日志条目。

有关详细信息，请参阅 [Azure Monitor 中的 Syslog 数据源](../azure-monitor/agents/data-sources-syslog.md)。

## <a name="configure-syslog-collection"></a>配置 Syslog 集合

### <a name="configure-your-linux-machine-or-appliance"></a>配置 Linux 计算机或设备

1. 在 Azure Sentinel 中，选择“数据连接器”，然后选择 Syslog 连接器。 

1. 在 Syslog 边栏选项卡中，选择“打开连接器页面”。 

1. 安装 Linux 代理。 在“选择代理安装位置：”下
    
    **对于 Azure Linux VM：**
      
    1. 选择“在 Azure Linux 虚拟机上安装代理”。
    
    1. 单击“下载并安装用于 Azure Linux 虚拟机的代理 >”链接。 
    
    1. 在“虚拟机”边栏选项卡中，单击要在其上安装代理的虚拟机，然后单击“连接”。  对于要连接的每个 VM，重复此步骤。
    
    **对于任何其他 Linux 计算机：**

    1. 选择“在非 Azure Linux 计算机上安装代理”

    1. 单击“下载并安装用于非 Azure Linux 虚拟机的代理 >”链接。 

    1. 在“代理管理”边栏选项卡中，单击“Linux 服务器”选项卡，然后复制用于“下载和载入适用于 Linux 的代理”的命令并在 Linux 计算机上运行。   
    
   > [!NOTE]
   > 请确保根据组织的安全策略配置这些计算机的安全性设置。 例如，可以配置网络设置使其符合组织的网络安全策略，并更改守护程序中的端口和协议，使其符合安全要求。

### <a name="configure-the-log-analytics-agent"></a>配置 Log Analytics 代理

1. 在 Syslog 连接器边栏选项卡底部，单击“打开工作区代理配置 >”链接。

1. 在“代理配置”边栏选项卡上，选择 Syslog 选项卡。然后，添加要让连接器收集的设施。  选择“添加设施”，然后从设施下拉列表中选择。
    
    - 添加 syslog 设备包括在其日志标头中的设施。 
    
    - 如果要将异常 SSH 登录检测与收集的数据一起使用，请添加 auth 和 authpriv。  有关其他详细信息，请参阅[以下部分](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)。

1. 如果已添加要监视的所有设施，请验证是否已标记所有所需严重级别的复选框。

1. 选择“应用”。 

1. 在 VM 或设备上，请确保正在发送指定的设施。

1. 要在“日志”中查询 syslog 日志数据，请在查询窗口中键入 `Syslog`。

1. 可以使用[在 Azure Monitor 日志查询中使用函数](../azure-monitor/logs/functions.md)中所述的查询参数来分析 Syslog 消息。 然后，可以将查询另存为新的 Log Analytics 函数，并将其用作新的数据类型。

> [!NOTE]
> **使用同一台计算机转发纯 Syslog 和 CEF 消息**
>
> 也可以使用现有的 [CEF 日志转发器计算机](connect-cef-agent.md)从普通 Syslog 源收集和转发日志。 但是，必须执行以下步骤以避免以这两种格式将事件发送到 Azure Sentinel，因为这将导致事件重复。
>
>    已根据 [CEF 源设置了数据收集](connect-common-event-format.md)，并按上述方式配置 Log Analytics 代理：
>
> 1. 在以 CEF 格式发送日志的每台计算机上，必须编辑 Syslog 配置文件，以删除用于发送 CEF 消息的设施。 这样一来，在 CEF 中发送的设施也不会在 Syslog 中发送。 有关如何执行此操作的详细说明，请参阅[在 Linux 代理上配置 Syslog](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent)。
>
> 1. 必须在这些计算机上运行以下命令，才能在 Azure Sentinel 中通过 Syslog 配置禁用代理的同步。 这可确保不会覆盖你在上一步中所做的配置更改。<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>为异常 SSH 登录检测配置 Syslog 连接器

> [!IMPORTANT]
> 异常 SSH 登录检测目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

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

{"mode":"full","isActive":false}