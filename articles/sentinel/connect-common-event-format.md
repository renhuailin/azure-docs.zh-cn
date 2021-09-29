---
title: 将设备中 CEF 格式的日志引入到 Azure Sentinel | Microsoft Docs
description: 使用基于 Linux 的日志转发器上安装的 Log Analytics 代理，将通过 Syslog 以通用事件格式 (CEF) 发送的日志引入到 Azure Sentinel 工作区。
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
ms.date: 07/26/2021
ms.author: yelevin
ms.openlocfilehash: 71f127bd45c833205067256a2b23cdc72b2c3bfb
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784501"
---
# <a name="get-cef-formatted-logs-from-your-device-or-appliance-into-azure-sentinel"></a>将设备中 CEF 格式的日志引入到 Azure Sentinel

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

许多网络设备和安全设备通过 Syslog 协议以一种称作通用事件格式 (CEF) 的专用格式发送其系统日志。 此格式包含的信息比标准 Syslog 格式更丰富，它以分析后的键-值排列方式显示信息。 Log Analytics 代理接受 CEF 日志并专门将其设置为可在 Azure Sentinel 中使用的格式，然后将其转发到 Azure Sentinel 工作区。

本文介绍使用 CEF 格式的日志连接数据源的过程。 有关使用此方法的数据连接器的信息，请参阅 [Azure Sentinel 连接器参考](data-connectors-reference.md)。

建立此连接需要执行两个主要步骤，下面将会对其进行详述：

- 将某个 Linux 计算机或 VM 指定为专用日志转发器，在其上安装 Log Analytics 代理，然后将该代理配置为向 Azure Sentinel 工作区转发日志。
代理的安装和配置由部署脚本来处理。

- 将设备配置为以 CEF 格式将其日志发送到 Syslog 服务器。

> [!NOTE]
> 数据存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="supported-architectures"></a>支持的体系结构

下图描绘了在 Azure 中使用 Linux VM 时的设置：

 ![Azure 中的 CEF](./media/connect-cef/cef-syslog-azure.png)

或者，如果你在其他云或本地计算机上使用 VM，则需要使用以下设置：

 ![本地 CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="prerequisites"></a>先决条件

需要 Azure Sentinel 工作区才能将 CEF 数据引入 Log Analytics。

- 你必须对此工作区拥有读取和写入权限。

- 你必须对该工作区的共享密钥拥有读取权限。 [详细了解工作区密钥](../azure-monitor/agents/agent-windows.md)。

## <a name="designate-a-log-forwarder-and-install-the-log-analytics-agent"></a>指定日志转发器并安装 Log Analytics 代理

本部分介绍如何指定并配置 Linux 计算机，该计算机会将设备中的日志转发到 Azure Sentinel 工作区。

该 Linux 计算机可以是位于本地环境、Azure VM 或其他云中的 VM 上的物理计算机或虚拟机。

使用通用事件格式 (CEF) 数据连接器页上提供的链接在指定的计算机上运行一个脚本并执行以下任务：

- 安装适用于 Linux 的 Log Analytics 代理（也称为 OMS 代理），并对其进行配置以实现以下目的：
    - 在 TCP 端口 25226 上侦听来自内置 Linux Syslog 守护程序的 CEF 消息
    - 通过 TLS 将消息安全地发送到 Azure Sentinel 工作区，在其中对其进行分析和扩充

- 配置内置 Linux Syslog 守护程序 (rsyslog.d/syslog-ng) 以实现以下目的：
    - 在 TCP 端口 514 上侦听来自安全解决方案的 Syslog 消息
    - 使用 TCP 端口 25226 仅将其标识为 CEF 的消息转发到 localhost 上的 Log Analytics 代理

有关详细信息，请参阅[部署日志转发器以将 Syslog 和 CEF 日志引入到 Azure Sentinel](connect-log-forwarder.md)。

### <a name="security-considerations"></a>安全注意事项

确保根据组织的安全策略配置计算机的安全性。 例如，你可以根据企业网络安全策略配置网络，并根据自己的要求更改守护程序中的端口和协议。

有关详细信息，请参阅[在 Azure 中保护 VM](../virtual-machines/security-policy.md) 和[网络安全最佳做法](../security/fundamentals/network-best-practices.md)。

如果设备通过 TLS 发送 Syslog 和 CEF 日志（例如，当日志转发器位于云中时），则你需要配置 Syslog 守护程序（rsyslog 或 syslog-ng）以便通过 TLS 进行通信。 

有关详细信息，请参阅：

- [使用 TLS 加密 Syslog 流量 - rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [使用 TLS 加密日志消息 - syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

## <a name="configure-your-device"></a>配置设备

找到并遵循设备供应商的配置说明将 CEF 格式的日志发送到 SIEM 或日志服务器。 

如果你的产品已出现在数据连接器库中，你可以查阅 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)以获取帮助，其中的配置说明应包括了以下列表中的设置。

   - 协议 = TCP
   - 端口 = 514
   - 格式 = CEF
   - IP 地址 - 确保将 CEF 消息发送到专用于此目的的虚拟机 IP 地址。

此解决方案支持 Syslog RFC 3164 或 RFC 5424。

> [!TIP]
> 根据需要在设备中定义不同的协议或端口号，但还必须在日志转发器上的 Syslog 守护程序中做出相同的更改。
>

## <a name="find-your-data"></a>查找数据

建立连接后，最多可能需要 20 分钟，数据才会显示在 Log Analytics 中。

若要在 Log Analytics 中搜索 CEF 事件，请在查询窗口中查询 `CommonSecurityLog` 表。

数据连接器库中列出的某些产品需要使用其他分析器才能发挥最佳效果。 这些分析器是通过使用 Kusto 函数实现的。 有关详细信息，请参阅 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)页上适用于你的产品的部分。

若要查找这些产品的 CEF 事件，请输入 Kusto 函数的名称（而不是“CommonSecurityLog”）作为查询主题。

可以在 Azure Sentinel 门户中你产品的数据连接器页的“后续步骤”选项卡上，找到为专门为你的产品创建的有用示例查询、工作簿和分析规则模板。

如果看不到任何数据，请参阅 [CEF 故障排除](./troubleshooting-cef-syslog.md)页中的指导。

### <a name="changing-the-source-of-the-timegenerated-field"></a>更改 TimeGenerated 字段的源

默认情况下，Log Analytics 代理使用其从 Syslog 守护程序接收到事件的时间来填充架构中的 TimeGenerated 字段。 因此，在源系统上生成事件的时间不会记录在 Azure Sentinel 中。

但是，可以运行以下命令，该命令将下载并运行 `TimeGenerated.py` 脚本。 该脚本将 Log Analytics 代理配置为在其源系统上使用事件的原始时间（而不是代理接收事件的时间）填充 TimeGenerated 字段。

```bash
wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
```

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解 Azure Sentinel 如何从设备收集 CEF 日志。 若要详细了解如何将产品连接到 Azure Sentinel，请参阅以下文章：

- [部署 Syslog/CEF 转发器](connect-log-forwarder.md)
- [Azure Sentinel 数据连接器参考](data-connectors-reference.md)
- [排查日志转发器连接问题](troubleshooting-cef-syslog.md#validate-cef-connectivity)

若要详细了解如何处理 Azure Sentinel 中收集的数据，请参阅以下文章：

- 了解 [CEF 和 CommonSecurityLog 字段映射](cef-name-mapping.md)。
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。