---
title: 将 Apache HTTP 服务器连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Apache HTTP 服务器连接器将 Apache 日志请求到 Azure Sentinel。 在工作簿中查看 Apache 数据，创建警报，并改进调查。
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 59ef34f9d6d42277d8e83bf4e0ebb8ee29096f52
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566715"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>将 Apache HTTP 服务器连接到 Azure Sentinel

> [!IMPORTANT]
> Apache HTTP 服务器连接器目前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍如何将 Apache HTTP 服务器连接到 Azure Sentinel。 Apache HTTP 服务器连接器可让你轻松地将 Apache HTTP 服务器日志引入 Azure Sentinel，使你可以查看工作簿中的数据，对其进行查询以创建自定义警报，并结合它来改善调查。 Apache HTTP 服务器和 Azure Sentinel 之间的集成利用 Log Analytics 代理进行本地文件处理。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 您必须对 Azure Sentinel 工作区具有写入权限。

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>通过 Log Analytics 代理配置和集成 Apache HTTP 服务器日志

配置 Apache HTTP 服务器日志文件位置，通过 Log Analytics 代理将数据转发到 Azure 工作区。
配置 Log Analytics 代理以读取 Apache HTTP 服务器日志文件。

1. 按照中的说明 https://httpd.apache.org/docs/2.4/logs.html 设置 APACHE HTTP 服务器中的日志文件位置。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器** "，然后选择 " **Apache HTTP 服务器 (预览")**。

1. 选择 " **打开连接器" 页面**。

1. 按照 Apache HTTP 服务器页面上的说明进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "ApacheHTTPServer_CL 下的 Log Analytics 中。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Apache HTTP 服务器连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
