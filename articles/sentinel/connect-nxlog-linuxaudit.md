---
title: 将 NXLog LinuxAudit 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 NXLog LinuxAudit 数据连接器将 LinuxAudit 日志拉取到 Azure Sentinel 中。 在工作簿中查看 LinuxAudit 数据，创建警报，并改进调查。
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 6b10a0c4b1a655d998d9a418dde679c0c6b68b10
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252925"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>将 NXLog LinuxAudit 连接到 Azure Sentinel

> [!IMPORTANT]
> NXLog LinuxAudit 连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

借助 [NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) 连接器，可以轻松地将 Linux 安全事件实时导出到 Azure Sentinel，以便深入了解整个组织的域名服务器活动。 NXLog LinuxAudit 模块支持自定义审核规则，无需 auditd 或任何其他用户空间软件即可收集日志。 IP 地址和组/用户 ID 可解析为各自的名称，使 [Linux 审核](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html)日志对安全分析师而言更加明了。 NXLog LinuxAudit 与 Azure Sentinel 之间的集成可通过 REST API 来实现。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区所在的地理位置。

## <a name="configure-and-connect-nxlog-linuxaudit"></a>配置并连接 NXLog LinuxAudit

NXLog 可以配置为直接向 Azure Sentinel 发送 JSON 格式的事件。

1. 在 Azure Sentinel 门户中，单击“数据连接器”，然后选择“NXLog LinuxAudit”连接器。

1. 选择“打开连接器页”。

1. 按照“NXLog 用户指南”集成主题 [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) 中的分步说明操作，以配置通过 REST API 进行转发。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会显示在 LinuxAudit_CL 表的“自定义日志”部分的“日志”中。 

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 NXLog LinuxAudit 将 Linux 安全事件引入 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。