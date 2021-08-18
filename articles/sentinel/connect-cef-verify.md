---
title: 验证 Azure Sentinel 的连接性 | Microsoft Docs
description: 验证安全解决方案的连接性，以确保将 CEF 消息转发到 Azure Sentinel。
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
ms.date: 01/05/2021
ms.author: yelevin
ms.openlocfilehash: 98d97bd7c8ffab685475f50130d68668fe1c9f8b
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253288"
---
# <a name="step-3-validate-connectivity"></a>步骤 3：验证连接性

部署日志转发器（步骤 1）并配置安全解决方案以向其发送 CEF 消息（步骤 2）后，请按照以下说明验证安全解决方案与 Azure Sentinel 之间的连接性。

## <a name="prerequisites"></a>先决条件

- 必须在日志转发器计算机上拥有提升的权限 (sudo)。

- 必须在日志转发器计算机上安装 python 2.7 或 3 。<br>
使用 `python –version` 命令进行检查。

- 在此过程中的某个时间点，可能需要工作区 ID 和工作区工作区。 可以在工作区资源的“代理管理”下找到它们。

## <a name="how-to-validate-connectivity"></a>如何验证连接性

1. 从 Azure Sentinel 导航菜单中打开“日志”。 使用 CommonSecurityLog 架构运行一个查询，检查是否正在接收来自安全解决方案的日志。<br>
请注意，可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

1. 如果没有从查询中看到任何结果，请验证是否正从安全解决方案生成（或尝试生成部分）事件，并验证这些事件是否被转发到你指定的 Syslog 转发器计算机。

1. 在日志转发器上运行以下脚本（应用工作区 ID 代替占位符）以检查安全解决方案、日志转发器和 Azure Sentinel 之间的连接。 此脚本检查守护程序是否正在侦听正确的端口，转发配置是否正确以及是否有任何内容阻止守护程序与 Log Analytics 代理之间的通信。 它还会发送模拟消息“TestCommonEventFormat”以检查端到端连接。 <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - 你可能会收到一条消息，指示你运行命令以更正与“计算机”字段的映射有关的问题。 有关详细信息，请参阅[验证脚本中的说明](#mapping-command)。

    - 你可能会收到一条消息，指示你运行命令以更正与“解析 Cisco ASA 防火墙日志”有关的问题。 有关详细信息，请参阅[验证脚本中的说明](#parsing-command)。

## <a name="validation-script-explained"></a>验证脚本说明

验证脚本执行以下检查：

# <a name="rsyslog-daemon"></a>[rsyslog 守护程序](#tab/rsyslog)

1. 检查文件<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    是否存在并有效。

1. 检查文件是否包含以下文本：

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. 使用以下命令检查是否已按预期配置对 Cisco ASA 防火墙事件的解析：

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>如果解析存在问题，脚本将生成一条错误消息，指示你手动运行以下命令（将工作区 ID 替换为占位符）。 此命令将确保正确解析并重启代理。

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 使用以下命令检查 syslog 源中的“计算机”字段是否已正确映射到 Log Analytics 代理中：

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>如果映射存在问题，脚本将生成一条错误消息，指示你手动运行以下命令（将工作区 ID 替换为占位符）。 此命令将确保正确映射并重启代理。

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 检查计算机上是否存在任何可能阻止网络流量的安全增强功能（例如主机防火墙）。

1. 检查 syslog 守护程序 (rsyslog) 是否已正确配置为将被标识为 CEF 的消息发送到 TCP 端口 25226 上的 Log Analytics 代理：

    - 配置文件：`/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
        ```

1. 重启 syslog 守护程序和 Log Analytics 代理：

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 检查是否建立了必需的连接：用于接收数据的 tcp 514、用于 syslog 守护程序和 Log Analytics 代理之间的内部通信的 tcp 25226：

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. 检查 syslog 守护程序是否在端口 514 上接收数据，以及代理是否在端口 25226 上接收数据：

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. 将 MOCK 数据发送到 localhost 上的端口 514。 通过运行以下查询，应在 Azure Sentinel 工作区中观察到此数据：

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[ 守护程序](#tab/syslogng)

1. 检查文件<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    是否存在并有效。

1. 检查文件是否包含以下文本：

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. 使用以下命令检查是否已按预期配置对 Cisco ASA 防火墙事件的解析：

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>如果解析存在问题，脚本将生成一条错误消息，指示你手动运行以下命令（将工作区 ID 替换为占位符）。 此命令将确保正确解析并重启代理。

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 使用以下命令检查 syslog 源中的“计算机”字段是否已正确映射到 Log Analytics 代理中：

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>如果映射存在问题，脚本将生成一条错误消息，指示你手动运行以下命令（将工作区 ID 替换为占位符）。 此命令将确保正确映射并重启代理。

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 检查计算机上是否存在任何可能阻止网络流量的安全增强功能（例如主机防火墙）。

1. 检查 syslog 守护程序 (syslog-ng) 是否已正确配置为将被标识为 CEF（使用正则表达式）的消息发送到 TCP 端口 25226 上的 Log Analytics 代理：

    - 配置文件：`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. 重启 syslog 守护程序和 Log Analytics 代理：

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 检查是否建立了必需的连接：用于接收数据的 tcp 514、用于 syslog 守护程序和 Log Analytics 代理之间的内部通信的 tcp 25226：

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. 检查 syslog 守护程序是否在端口 514 上接收数据，以及代理是否在端口 25226 上接收数据：

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. 将 MOCK 数据发送到 localhost 上的端口 514。 通过运行以下查询，应在 Azure Sentinel 工作区中观察到此数据：

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---


## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解 [CEF 和 CommonSecurityLog 字段映射](cef-name-mapping.md)。
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。