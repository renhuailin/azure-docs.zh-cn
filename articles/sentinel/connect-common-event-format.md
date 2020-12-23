---
title: 将 CEF 数据连接到 Azure Sentinel Preview |Microsoft Docs
description: 使用 Linux 计算机作为日志转发器，将发送常见事件格式 (CEF) 消息的外部解决方案连接到 Azure Sentinel。
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
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 4b34477a40530a0e6f26b59dd9707c019418b2a4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655844"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>使用通用事件格式连接外部解决方案

当你连接用于发送 CEF 消息的外部解决方案时，使用 Azure Sentinel 连接三个步骤：

步骤1： [通过部署 Syslog/CEF 转发器步骤2连接 CEF](connect-cef-agent.md) ： [执行特定于解决方案的步骤](connect-cef-solution-config.md) 步骤3： [验证连接性](connect-cef-verify.md)

本文介绍了连接的工作原理，列出了先决条件，并演示了部署安全解决方案机制以发送常见事件格式 (CEF) 消息在 Syslog 顶部的步骤。 

> [!NOTE] 
> 数据存储在运行 Azure Sentinel 的工作区的地理位置。

若要进行此连接，需要部署 Syslog 转发器服务器，以支持设备与 Azure Sentinel 之间的通信。  服务器由专用 Linux 计算机 (VM 或本地) ，其中安装了适用于 Linux 的 Log Analytics 代理。 

下图介绍了在 Azure 中使用 Linux VM 时的设置：

 ![Azure 中的 CEF](./media/connect-cef/cef-syslog-azure.png)

或者，如果你在另一个云中或在本地计算机上使用 VM，则会存在此设置： 

 ![本地 CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>安全注意事项

请确保根据组织的安全策略配置计算机的安全性。 例如，你可以将网络配置为与你的企业网络安全策略一致，并更改守护程序中的端口和协议以符合你的要求。 你可以使用以下说明来改善计算机安全配置：  [Azure 中的安全 VM](../virtual-machines/security-policy.md)、 [网络安全的最佳做法](../security/fundamentals/network-best-practices.md)。

若要在 Syslog 源和 Syslog 转发器之间使用 TLS 通信，则需要将 Syslog 守护程序 (rsyslog 或 syslog-ng) 配置为在 TLS 中进行通信： [使用 Rsyslog 加密 Syslog 流量](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)，使用 [tls 加密日志消息– Syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)。
 
## <a name="prerequisites"></a>先决条件

请确保用作日志转发器的 Linux 计算机运行下列操作系统之一：

- 64 位
  - CentOS 7 和8，包括次要版本 (不是 6) 
  - Amazon Linux 2017.09
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 和8，包括次要版本 (而不是 6) 
  - Debian GNU/Linux 8 和 9
  - Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
  - SUSE Linux Enterprise Server 12，15

- 32 位
  - CentOS 7 和8，包括次要版本 (不是 6) 
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 和8，包括次要版本 (而不是 6) 
  - Debian GNU/Linux 8 和 9
  - Ubuntu Linux 14.04 LTS 和 16.04 LTS
 
- 守护程序版本
  - Syslog-ng： 2.1-3.22。1
  - Rsyslog： v8
  
- 支持的 Syslog Rfc
  - Syslog RFC 3164
  - Syslog RFC 5424
 
请确保您的计算机还满足以下要求： 

- 权限
  - 你的计算机上必须具有提升的权限 (sudo) 。 

- 软件要求
  - 请确保在计算机上运行 python 2.7 或3。

## <a name="next-steps"></a>后续步骤

本文档介绍了 Azure Sentinel 如何从安全解决方案和设备收集 CEF 日志。 若要了解如何将解决方案连接到 Azure Sentinel，请参阅以下文章：

- 步骤1： [通过部署 Syslog/CEF 转发器连接 CEF](connect-cef-agent.md)
- 步骤2： [执行特定于解决方案的步骤](connect-cef-solution-config.md)
- 步骤3： [验证连接性](connect-cef-verify.md)

若要详细了解如何处理在 Azure Sentinel 中收集的数据，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./tutorial-detect-threats-built-in.md)。