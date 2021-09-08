---
title: 部署日志转发器以将 Syslog 和 CEF 日志引入到 Azure Sentinel | Microsoft Docs
description: 了解如何部署由 Syslog 守护程序和 Log Analytics 代理组成的日志转发器，作为将 Syslog 和 CEF 日志引入到 Azure Sentinel 的过程的一部分。
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
ms.date: 01/05/2021
ms.author: bagol
ms.openlocfilehash: 5a7df5ba2de5b3d12e39b403be6a9e336734d144
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260914"
---
# <a name="deploy-a-log-forwarder-to-ingest-syslog-and-cef-logs-to-azure-sentinel"></a>部署日志转发器以将 Syslog 和 CEF 日志引入到 Azure Sentinel

若要将 Syslog 和 CEF 日志引入到 Azure Sentinel（尤其是从无法直接安装 Log Analytics 代理的设备进行引入），你需要指定并配置一台 Linux 计算机，用于从设备收集日志并将它们转发到 Azure Sentinel 工作区。 此计算机可以是位于本地环境、Azure VM 或其他云中的 VM 上的物理计算机或虚拟机。 

此计算机具有两个参与此过程的组件：

- 一个收集日志的 syslog 守护程序，可以是 rsyslog 或 syslog-ng 。
- 将日志转发到 Azure Sentinel 的 Log Analytics 代理（也称为 OMS 代理）。

使用下面提供的链接，可在指定的计算机上运行脚本，以执行以下任务：

- 安装适用于 Linux 的 Log Analytics 代理（也称为 OMS 代理），并对其进行配置以实现以下目的：
    - 在 TCP 端口 25226 上侦听来自内置 Linux Syslog 守护程序的 CEF 消息
    - 通过 TLS 将消息安全地发送到 Azure Sentinel 工作区，在其中对其进行分析和扩充

- 为实现以下目的，请配置内置的 Linux Syslog 守护程序 (rsyslog.d/syslog-ng)：
    - 在 TCP 端口 514 上侦听来自安全解决方案的 Syslog 消息
    - 使用 TCP 端口 25226 仅将其标识为 CEF 的消息转发到 localhost 上的 Log Analytics 代理
 
## <a name="prerequisites"></a>先决条件

你的计算机必须满足以下要求：

- **硬件（物理/虚拟）**

    - 你的 Linux 计算机必须至少有 4 个 CPU 核心和 8 GB RAM。

        > [!NOTE]
        > - 使用 rsyslog 守护程序的单台日志转发器计算机支持的最大容量为每秒收集 8500 个事件 (EPS) 。

- **操作系统**

    - CentOS 7 和 8（非版本 6），包括次要版本（64 位/32 位）
    - Amazon Linux 2017.09（仅限 64 位）
    - Oracle Linux 7（64 位/32 位）
    - Red Hat Enterprise Linux (RHEL) Server 7 和 8（非版本 6），包括次要版本（64 位/32 位）
    - Debian GNU/Linux 8 和 9（64 位/32 位）
    - Ubuntu Linux 14.04 LTS 和 16.04 LTS（64 位/32 位），以及 18.04 LTS（仅限 64 位）
    - SuSE Linux Enterprise Server 12、15（仅限 64 位）

- **守护程序版本**

    - Rsyslog：v8
    - Syslog-ng：2.1 - 3.22.1

- **包**
    - 必须在 Linux 计算机上安装 Python 2.7 或 3 。<br>使用 `python --version` 或 `python3 --version` 命令进行检查。

- **Syslog RFC 支持**
  - Syslog RFC 3164
  - Syslog RFC 5424
 
- **配置**
    - 必须在指定的 Linux 计算机上拥有提升的权限 (sudo)。
    - 安装 Log Analytics 代理之前，Linux 计算机不得连接到任何 Azure 工作区。

- **数据**
    - 在此过程中，你可能需要 Azure Sentinel 工作区的“工作区 ID”和“工作区主键” 。 可以在工作区设置的“代理管理”下找到它们。

### <a name="security-considerations"></a>安全注意事项

确保根据组织的安全策略配置计算机的安全性。 例如，你可以根据企业网络安全策略配置网络，并根据自己的要求更改守护程序中的端口和协议。 可使用以下说明来改善计算机安全配置：[在 Azure 中保护 VM](../virtual-machines/security-policy.md)、[网络安全最佳做法](../security/fundamentals/network-best-practices.md)。

如果设备通过 TLS 发送 Syslog 和 CEF 日志（例如由于日志转发器位于云中），则需要配置 Syslog 守护程序（rsyslog 或 syslog-ng）以在 TLS 中进行通信。 有关详细信息，请参阅以下文档：  
- [使用 TLS 加密 Syslog 流量 - rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [使用 TLS 加密日志消息 - syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

## <a name="run-the-deployment-script"></a>运行部署脚本
 
1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。 从连接器库中选择产品的连接器（如果未列出你的产品，则选择“通用事件格式(CEF)”），然后选择右下角的“打开连接器页面”按钮 。 

1. 在连接器页面上的“1.2 在 Linux 计算机上安装 CEF 收集器”下的说明中，复制“运行以下脚本以安装并应用 CEF 收集器”下提供的链接 。  
如果无法访问该页，请复制以下文本中的链接（复制并粘贴上面的“工作区 ID”和“主键”以替换占位符） ：

    ```bash
    sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]
    ```
1. 将链接或文本粘贴到日志转发器上的命令行中，然后运行它。

1. 脚本运行时，请检查以确保没有收到任何错误或警告消息。
    - 你可能会收到一条消息，指示你运行命令以更正与“计算机”字段的映射有关的问题。 有关详细信息，请参阅[部署脚本中的说明](#mapping-command)。

1. [配置设备以发送 CEF 消息](connect-common-event-format.md#configure-your-device)。

    > [!NOTE]
    > 使用同一台计算机转发纯 Syslog 和 CEF 消息
    >
    > 如果打算使用此日志转发器计算机转发 [Syslog 消息](connect-syslog.md)以及 CEF，则为避免将事件复制到 Syslog 和 CommonSecurityLog 表，请执行以下操作：
    >
    > 1. 在以 CEF 格式将日志发送到转发器的每台源计算机上，必须编辑 Syslog 配置文件来移除用于发送 CEF 消息的设备。 这样一来，在 CEF 中发送的设施也不会在 Syslog 中发送。 有关如何执行此操作的详细说明，请参阅[在 Linux 代理上配置 Syslog](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent)。
    >
    > 1. 必须在这些计算机上运行以下命令，才能在 Azure Sentinel 中通过 Syslog 配置禁用代理的同步。 这可确保不会覆盖你在上一步中所做的配置更改。<br>
    > `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="deployment-script-explained"></a>部署脚本说明

下面是针对部署脚本操作的逐条命令说明。

选择 syslog 守护程序，查看相应的描述。

# <a name="rsyslog-daemon"></a>[rsyslog 守护程序](#tab/rsyslog)

1. **下载并安装 Log Analytics 代理：**

    - 下载 Log Analytics (OMS) Linux 代理的安装脚本。

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - 安装 Log Analytics 代理。
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **将 Log Analytics 代理配置设置为侦听端口 25226 并将 CEF 消息转发到 Azure Sentinel：**

    - 从 Log Analytics 代理 GitHub 存储库下载配置。

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **配置 Syslog 守护程序：**

    - 使用 syslog 配置文件 `/etc/rsyslog.conf` 打开用于 TCP 通信的端口 514。

    - 通过将特殊的配置文件 `security-config-omsagent.conf` 插入 syslog 守护程序目录 `/etc/rsyslog.d/`，将守护程序配置为将 CEF 消息转发到 TCP 端口 25226 上的 Log Analytics 代理。

        `security-config-omsagent.conf` 文件的内容：

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **重启 Syslog 守护程序和 Log Analytics 代理：**

    - 重启 rsyslog 守护程序。
    
        ```bash
        service rsyslog restart
        ```

    - 重启 Log Analytics 代理。

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 验证“计算机”字段的映射是否符合预期：

    - 使用以下命令确保 syslog 源中的“计算机”字段已正确映射到 Log Analytics 代理中： 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>如果映射存在问题，脚本将生成一条错误消息，指示你手动运行以下命令（将工作区 ID 替换为占位符）。 此命令将确保正确映射并重启代理。
    
        ```bash
        sudo sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[syslog-ng 守护程序](#tab/syslogng)

1. **下载并安装 Log Analytics 代理：**

    - 下载 Log Analytics (OMS) Linux 代理的安装脚本。

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - 安装 Log Analytics 代理。
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **将 Log Analytics 代理配置设置为侦听端口 25226 并将 CEF 消息转发到 Azure Sentinel：**

    - 从 Log Analytics 代理 GitHub 存储库下载配置。

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **配置 Syslog 守护程序：**

    - 使用 syslog 配置文件 `/etc/syslog-ng/syslog-ng.conf` 打开用于 TCP 通信的端口 514。

    - 通过将特殊的配置文件 `security-config-omsagent.conf` 插入 syslog 守护程序目录 `/etc/syslog-ng/conf.d/`，将守护程序配置为将 CEF 消息转发到 TCP 端口 25226 上的 Log Analytics 代理。

        `security-config-omsagent.conf` 文件的内容：

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **重启 Syslog 守护程序和 Log Analytics 代理：**

    - 重启 syslog-ng 守护程序。
    
        ```bash
        service syslog-ng restart
        ```

    - 重启 Log Analytics 代理。

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 验证“计算机”字段的映射是否符合预期：

    - 使用以下命令确保 syslog 源中的“计算机”字段已正确映射到 Log Analytics 代理中： 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>如果映射存在问题，脚本将生成一条错误消息，指示你手动运行以下命令（将工作区 ID 替换为占位符）。 此命令将确保正确映射并重启代理。
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>后续步骤

本文档介绍了如何部署 Log Analytics 代理以将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解 [CEF 和 CommonSecurityLog 字段映射](cef-name-mapping.md)。
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。
