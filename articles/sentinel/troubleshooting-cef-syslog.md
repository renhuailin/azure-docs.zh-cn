---
title: 排查 Azure Sentinel 和 CEF 或 Syslog 数据连接器之间的连接故障 | Microsoft Docs
description: 了解如何排查并解决有关 Azure Sentinel CEF 或 Syslog 数据连接器的问题。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2021
ms.author: bagol
ms.openlocfilehash: 9f6585cec5c52dd2255fca2a31e2f92853954370
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129279006"
---
# <a name="troubleshoot-your-cef-or-syslog-data-connector"></a>排查 CEF 或 Syslog 数据连接器的故障

本文介绍用于为 Azure Sentinel 验证 CEF 或 Syslog 数据连接器并排查其故障的常见方法。

例如，如果日志未显示在 Azure Sentinel 中（无论是 Syslog 还是“通用安全日志”表中），则可能是数据源无法连接，或者有其他原因导致未引入数据。

连接器部署失败的其他表现包括 security_events.conf 或 security-omsagent.config.conf 文件丢失，或者 rsyslog 服务器没有侦听端口 514 。

有关更多信息，请参阅[使用通用事件格式连接外部解决方案](connect-common-event-format.md)和[使用 Syslog 从基于 Linux 的源收集数据](connect-syslog.md)。

> [!NOTE]
> 适用于 Windows 的 Log Analytics 代理通常称为 Microsoft Monitoring Agent (MMA)。 适用于 Linux 的 Log Analytics 代理通常称为“OMS 代理”。
>

> [!TIP]
> 进行故障排除时，建议按照本文中的步骤显示的顺序来检查和解决 Syslog 收集器、操作系统或 OMS 代理中的问题。
>
> 如果你使用与文档所述过程不同的方法部署了连接器，并且出现了问题，建议你清除部署并按照文档所述重新安装。
>

## <a name="validate-cef-connectivity"></a>验证 CEF 连接

[部署日志转发器](connect-common-event-format.md)并[配置安全解决方案以向其发送 CEF 消息](./connect-common-event-format.md)后，请使用该部分的步骤验证安全解决方案与 Azure Sentinel 之间的连接性。

1. 确保你已满足以下先决条件：

    - 必须在日志转发器计算机上拥有提升的权限 (sudo)。

    - 必须在日志转发器计算机上安装 python 2.7 或 3 。 使用 `python –version` 命令进行检查。

    - 在此过程中的某个时间点，可能需要工作区 ID 和工作区工作区。 可以在工作区资源的“代理管理”下找到它们。

1. 从 Azure Sentinel 导航菜单中打开“日志”。 使用 CommonSecurityLog 架构运行一个查询，检查是否正在接收来自安全解决方案的日志。

    可能需要大约 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

1. 如果没有从查询中看到任何结果，请验证是否正从安全解决方案生成（或尝试生成部分）事件，并验证这些事件是否被转发到你指定的 Syslog 转发器计算机。

1. 在日志转发器上运行以下脚本（应用工作区 ID 代替占位符）以检查安全解决方案、日志转发器和 Azure Sentinel 之间的连接。 此脚本检查守护程序是否正在侦听正确的端口，转发配置是否正确以及是否有任何内容阻止守护程序与 Log Analytics 代理之间的通信。 它还会发送模拟消息“TestCommonEventFormat”以检查端到端连接。 <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - 你可能会收到一条消息，指示你运行命令以更正与“计算机”字段的映射有关的问题。 有关详细信息，请参阅[验证脚本中的说明](#mapping-command)。

    - 你可能会收到一条消息，指示你运行命令以更正与“解析 Cisco ASA 防火墙日志”有关的问题。 有关详细信息，请参阅[验证脚本中的说明](#parsing-command)。

### <a name="cef-validation-script-explained"></a>CEF 验证脚本说明

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

## <a name="verify-cef-or-syslog-prerequisites"></a>验证 CEF 或 Syslog 先决条件

使用以下部分检查 CEF 或 Syslog 数据连接器先决条件。

### <a name="azure-virtual-machine-as-a-syslog-collector"></a>将 Azure 虚拟机用作 Syslog 收集器

如果使用 Azure 虚拟机作为 Syslog 收集器，请验证以下内容：

- 设置 Syslog 数据连接器时，请务必关闭对[MMA/OMS 代理](connect-windows-security-events.md#connector-options)的 [Azure 安全中心自动配置设置](../security-center/security-center-enable-data-collection.md)。

    完全设置数据连接器后，可以重新启用它们。

- 在部署[通用事件格式数据连接器 python 脚本](./connect-log-forwarder.md)之前，请确保虚拟机尚未连接到现有的 Syslog 工作区。 可以在 Log Analytics 工作区虚拟机列表中找到此信息，表中将连接到 Syslog 工作区的 VM 列为“已连接”。

- 确保 Azure Sentinel 已连接到正确的 Syslog 工作区，并安装了 SecurityInsights 解决方案。

    有关详细信息，请参阅[步骤 1：部署日志转发器](./connect-log-forwarder.md)。

- 请确保计算机的大小正确，至少满足最低要求的先决条件。 有关详细信息，请参阅 [CEF 先决条件](connect-common-event-format.md#prerequisites)。

### <a name="on-premises-or-a-non-azure-virtual-machine"></a>本地或非 Azure 虚拟机

如果对数据连接器使用本地计算机或非 Azure 虚拟机，请务必在受支持的 Linux 操作系统的新安装中运行安装脚本：

> [!TIP]
> 也可以在 Azure Sentinel 的通用事件格式数据连接器页面找到这个脚本。
>

```cli
sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py <WorkspaceId> <Primary Key>
```

### <a name="enable-your-syslog-facility-and-log-severity-collection"></a>启用 Syslog 设施和日志严重性合集

Syslog 服务器（rsyslog 或 syslog-ng）转发相关配置文件中定义的任何数据，这些数据由 Log Analytics 工作区中定义的设置自动填充。

请务必添加想要将其引入 Azure Sentinel 的设施和严重性日志级别的相关详细信息。 配置过程可能需要大约 20 分钟的时间。

有关详细信息，请参阅[部署脚本说明](./connect-log-forwarder.md#deployment-script-explained)和[在 Azure 门户中配置 Syslog](../azure-monitor/agents/data-sources-syslog.md)。


例如，对于 rsyslog 服务器，运行以下命令以显示 Syslog 转发的当前设置，并查看对配置文件的任何更改：

```bash
cat /etc/rsyslog.d/95-omsagent.conf
```

在这种情况下，对于 rsyslog，应显示类似于以下内容的输出。 此文件的内容应反映“Log Analytics 工作区客户端配置 - Syslog 设施设置”屏幕上的 Syslog 配置中的定义内容。


```bash
OMS Syslog collection for workspace c69fa733-da2e-4cf9-8d92-eee3bd23fe81
auth.=alert;auth.=crit;auth.=debug;auth.=emerg;auth.=err;auth.=info;auth.=notice;auth.=warning  @127.0.0.1:25224
authpriv.=alert;authpriv.=crit;authpriv.=debug;authpriv.=emerg;authpriv.=err;authpriv.=info;authpriv.=notice;authpriv.=warning  @127.0.0.1:25224
cron.=alert;cron.=crit;cron.=debug;cron.=emerg;cron.=err;cron.=info;cron.=notice;cron.=warning  @127.0.0.1:25224
local0.=alert;local0.=crit;local0.=debug;local0.=emerg;local0.=err;local0.=info;local0.=notice;local0.=warning  @127.0.0.1:25224
local4.=alert;local4.=crit;local4.=debug;local4.=emerg;local4.=err;local4.=info;local4.=notice;local4.=warning  @127.0.0.1:25224
syslog.=alert;syslog.=crit;syslog.=debug;syslog.=emerg;syslog.=err;syslog.=info;syslog.=notice;syslog.=warning  @127.0.0.1:25224
```


对于 rsyslog 服务器的 CEF 转发，运行以下命令以显示 Syslog 转发的当前设置，并查看对配置文件的任何更改：

```bash
cat /etc/rsyslog.d/security-config-omsagent.conf
```

在这种情况下，对于 rsyslog，应显示类似于以下内容的输出：

```bash
if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
```

## <a name="troubleshoot-operating-system-issues"></a>排查并解决操作系统问题

此过程介绍如何排查并解决自操作系统配置派生出来的问题。

**若要排查并解决操作系统问题，请执行以下操作**：

1. 如果没有进行过操作，请验证你使用的操作系统和 Python 版本是否受支持。 有关详细信息，请参阅 [CEF 先决条件](connect-common-event-format.md#prerequisites)和[配置你的 Linux 计算机或设备](connect-syslog.md#configure-your-linux-machine-or-appliance)。

1. 如果虚拟机位于 Azure 中，请验证网络安全组 (NSG) 是否允许在端口 514 上建立来自日志客户端 (Sender) 的入站 TCP/UDP 连接。

1. 验证数据包是否到达 Syslog 收集器。 若要捕获到达 Syslog 收集器的 syslog 数据包，请运行：

    ```config
    tcpdump -Ani any port 514 and host <ip_address_of_sender> -vv
    ```

1. 执行下列操作之一：

    - 如果看不到任何数据包到达，请确认 NSG 安全组权限以及 Syslog 收集器的路由路径。

    - 如果看到数据包到达，请确认它们不会被拒绝。

    如果看到被拒绝的数据包，请确认 IP 表没有阻止连接。

    若要确认数据包是否被拒绝，请运行：

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. 验证 Syslog 服务器是否正在处理日志。 运行：

    ```config
    tail -f /var/log/messages or tail -f /var/log/syslog
    ```

    任何正在处理的 Syslog 或 CEF 日志均以纯文本显示。

1. 请确认 rsyslog 服务器正在侦听 TCP/UDP 端口 514。 运行：

    ```config
    netstat -anp | grep syslog
    ```

    如果有任何 CEF 或 ASA 日志发送到 Syslog 收集器，应会看到 TCP 端口 25226 上已建立的连接。

    例如：

    ```config
    0 127.0.0.1:36120 127.0.0.1:25226 ESTABLISHED 1055/rsyslogd
    ```

    如果连接被阻止，可能是[与 OMS 代理的 SELinux 连接被阻止](#selinux-blocking-connection-to-the-oms-agent)，或者是[防火墙进程被阻止](#blocked-firewall-policy)。 使用以下说明集来确定问题。

### <a name="selinux-blocking-connection-to-the-oms-agent"></a>SELinux 阻止与 OMS 代理的连接

此过程介绍如何确认 SELinux 当前是否处于 `permissive` 状态，或是否阻止与 OMS 代理的连接。 当操作系统是 RedHat 或 CentOS 的分发时，此过程是相关的。

> [!NOTE]
> Azure Sentinel 对 CEF 和 Syslog 的支持仅包括 FIPS 强化。 目前不支持其他强化方法（如 SELinux 或 CIS）。
>

1. 运行：

    ```config
    sestatus
    ```

    状态将显示为以下其中之一：

    - `disabled`. 此配置支持连接到 Azure Sentinel。
    - `permissive`. 此配置支持连接到 Azure Sentinel。
    - `enforced`. 不支持此配置，必须禁用状态或将其设置为 `permissive`。

1. 如果状态当前设置为 `enforced`，则暂时将其关闭以确认这是否为阻止程序。 运行：

    ```config
    setenforce 0
    ```

    > [!NOTE]
    > 此步骤仅在服务器重新启动之前关闭 SELinux。 修改 SELinux 配置以将其保持关闭状态。
    >

1. 若要验证更改是否成功，请运行：

    ```
    getenforce
    ```

    应返回 `permissive` 状态。

> [!IMPORTANT]
> 重新启动系统时，此设置更新将丢失。 若要将此设置永久更新为 `permissive`，请修改 /etc/selinux/config 文件，将 `SELINUX` 值更改为 `SELINUX=permissive`。
>
> 有关详细信息，请参阅[RedHat 文档](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_selinux/changing-selinux-states-and-modes_using-selinux)。


### <a name="blocked-firewall-policy"></a>阻止的防火墙策略

此过程介绍如何验证防火墙策略是否阻止从 Rsyslog 守护程序到 OMS 代理的连接，以及根据需要禁用它的方法。


1. 运行以下命令来验证 IP 表中是否有拒绝项，这些拒绝项表示防火墙策略丢弃的流量：

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. 若要使防火墙策略保持启用状态，请创建一个策略规则以允许连接。 根据需要添加规则，以使 TCP/UDP 端口 25226 和 25224 能够通过活动防火墙。

    例如：

    ```config
    Every 2.0s: iptables -nvL                      rsyslog: Wed Jul  7 15:56:13 2021

    Chain INPUT (policy ACCEPT 6185K packets, 2466M bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain OUTPUT (policy ACCEPT 6792K packets, 6348M bytes)
     pkts bytes target     prot opt in     out     source               destination
    ```

1. 若要创建规则以使 TCP/UDP 端口 25226 和 25224 能够通过活动防火墙，请根据需要添加规则。

    1. 若要安装防火墙策略编辑器，请运行：

        ```config
        yum install policycoreutils-python
        ```

    1. 将防火墙规则添加到防火墙策略。 例如：

        ```config
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25226  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p udp --dport 25224  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25224  -j ACCEPT
        ```

    1. 验证是否添加了例外。 运行：

        ```config
        sudo firewall-cmd --direct --get-rules ipv4 filter INPUT
        ```

    1. 重新加载防火墙。 运行：

        ```config
        sudo firewall-cmd --reload
        ```

> [!NOTE]
> 若要禁用防火墙，请运行：`sudo systemctl disable firewalld`
>

## <a name="linux-and-oms-agent-related-issues"></a>与 Linux 和 OMS 代理相关的问题

如果本文前面所述的步骤无法解决问题，则 OMS 代理和 Azure Sentinel 工作区之间的连接存在问题。

在这种情况下，请验证以下内容以继续故障排除：

- 确保在 Syslog 收集器上能够看到到达 TCP/UDP 端口 514 的数据包

- 确保可以看到日志被写入到本地日志文件（/var/log/messages 或 /var/log/syslog） 

- 确保可以看到数据包在端口 25224 和/或 25226 上流动

- 确保虚拟机通过 TCP 与端口 443 建立出站连接，或者可以连接到 [Log Analytics 终结点](../azure-monitor/agents/log-analytics-agent.md#network-requirements)

- 确保可以通过防火墙策略从 Syslog 收集器访问所需的 URL。 有关详细信息，请参阅 [Log Analytics 代理防火墙要求](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)。

- 请确保 Azure 虚拟机在工作区的虚拟机列表中显示为“已连接”。

运行以下命令，确定代理是否与 Azure 成功通信，或者 OMS 代理是否被阻止连接到 Log Analytics 工作区。

```config
Heartbeat
 | where Computer contains "<computername>"
 | sort by TimeGenerated desc
```

如果代理成功通信，则会返回日志条目。 否则，OMS 代理可能会受阻。

## <a name="next-steps"></a>后续步骤

如果本文中的故障排除步骤无法帮助你解决问题，请打开支持工单或使用 Azure Sentinel 社区资源。 有关详细信息，请参阅[对使用 Azure Sentinel 有用的资源](resources.md)。

要详细了解 Azure Sentinel，请参阅以下文章：

- 了解 [CEF 和 CommonSecurityLog 字段映射](cef-name-mapping.md)。
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。
