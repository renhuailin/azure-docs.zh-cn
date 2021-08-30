---
title: 将 CEF 数据连接到 Azure Sentinel | Microsoft Docs
description: 使用 Linux 计算机作为日志转发器，将发送通用事件格式 (CEF) 消息的外部解决方案连接到 Azure Sentinel。
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
ms.openlocfilehash: 7cf50ee7b165fec445f65d3e57e229f04db5320b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695143"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>连接使用通用事件格式的外部解决方案

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

连接发送 CEF 消息的外部解决方案时，会执行三个步骤来连接 Azure Sentinel：

步骤 1：[通过部署 Syslog/CEF 转发器连接 CEF](connect-cef-agent.md)；步骤 2：[执行特定于解决方案的步骤](connect-cef-solution-config.md)；步骤 3：[验证连接](troubleshooting-cef-syslog.md#validate-cef-connectivity)

本文将介绍连接工作原理，列出先决条件，并介绍为安全解决方案部署一个机制以基于 Syslog 发送通用事件格式 (CEF) 消息的步骤。 

> [!NOTE] 
> 数据存储在运行 Azure Sentinel 的工作区的地理位置。

若要建立此连接，需要部署一个 Syslog 转发器服务器，以支持设备与 Azure Sentinel 之间的通信。  该服务器包含一个专用的 Linux 计算机（VM 或本地计算机），其中安装了适用于 Linux 的 Log Analytics 代理。 

下图描绘了在 Azure 中使用 Linux VM 时的设置：

 ![Azure 中的 CEF](./media/connect-cef/cef-syslog-azure.png)

或者，如果使用另一个云中的 VM 或使用本地计算机，则存在以下设置： 

 ![本地 CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>安全注意事项

确保根据组织的安全策略配置计算机的安全性。 例如，你可以根据企业网络安全策略配置网络，并根据自己的要求更改守护程序中的端口和协议。 可使用以下说明来改善计算机安全配置：[在 Azure 中保护 VM](../virtual-machines/security-policy.md)、[网络安全最佳做法](../security/fundamentals/network-best-practices.md)。

若要在 Syslog 源与 Syslog 转发器之间使用 TLS 通信，需将 Syslog 守护程序（rsyslog 或 syslog-ng）配置为以 TLS 通信：[使用 TLS 加密 Syslog 流量 - rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)、[使用 TLS 加密日志消息 – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)。
 
## <a name="prerequisites"></a>先决条件

需要 Azure Sentinel 工作区才能将 CEF 数据引入 Log Analytics。

确保用作日志转发器的 Linux 计算机运行的是以下操作系统之一：

- 64 位
  - CentOS 7 和 8，包括次要版本（不是版本 6）
  - Amazon Linux 2017.09
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 和 8，包括次要版本（不是版本 6）
  - Debian GNU/Linux 8 和 9
  - Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
  - SUSE Linux Enterprise Server 12、15

- 32 位
  - CentOS 7 和 8，包括次要版本（不是版本 6）
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 和 8，包括次要版本（不是版本 6）
  - Debian GNU/Linux 8 和 9
  - Ubuntu Linux 14.04 LTS 和 16.04 LTS
 
- 守护程序版本
  - Syslog-ng：2.1 - 3.22.1
  - Rsyslog：v8
  
- 支持 Syslog RFC
  - Syslog RFC 3164
  - Syslog RFC 5424
 
确保计算机还满足以下要求： 

- 容量
  - 计算机必须至少有 4 个 CPU 核心和 8 GB RAM。

    > [!NOTE]
    > - 使用 rsyslog 守护程序的单台日志转发器计算机支持的最大容量为每秒收集 8500 个事件 (EPS) 。

- 权限
  - 你必须在计算机上拥有提升的权限 (sudo)。 

- 软件要求
  - 确保计算机运行的是 Python 2.7 或 3。

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解 Azure Sentinel 如何从安全解决方案和设备收集 CEF 日志。 若要了解如何将解决方案连接到 Azure Sentinel，请参阅以下文章：

- 步骤 1：[通过部署 Syslog/CEF 转发器连接 CEF](connect-cef-agent.md)
- 步骤 2：[执行特定于解决方案的步骤](connect-cef-solution-config.md)
- 步骤 3：[验证连接](troubleshooting-cef-syslog.md#validate-cef-connectivity)

若要详细了解如何处理 Azure Sentinel 中收集的数据，请参阅以下文章：

- 了解 [CEF 和 CommonSecurityLog 字段映射](cef-name-mapping.md)。
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。