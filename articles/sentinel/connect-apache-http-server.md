---
title: 将 Apache HTTP 服务器连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Apache HTTP 服务器连接器将 Apache 日志拉取到 Azure Sentinel 中。 在工作簿中查看 Apache 数据，创建警报，并改进调查。
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
ms.openlocfilehash: 6d93854d47c65b500f280107e0bf6d0af3f80681
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252591"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>将 Apache HTTP 服务器连接到 Azure Sentinel

> [!IMPORTANT]
> Apache HTTP 服务器连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍了如何将 Apache HTTP 服务器连接到 Azure Sentinel。 使用 Apache HTTP 服务器连接器，可以轻松地将 Apache HTTP 服务器日志引入到 Azure Sentinel 中，这样，你就可以在工作簿中查看数据，可以查询这些数据以创建自定义警报，并且可以整合这些数据来改进调查。 Apache HTTP 服务器和 Azure Sentinel 之间的集成利用 Log Analytics 代理提供的本地文件处理功能。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 必须拥有对 Azure Sentinel 工作区的写入权限。

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>通过 Log Analytics 代理配置和集成 Apache HTTP 服务器日志

将 Apache HTTP 服务器配置为通过 Log Analytics 代理将日志文件发送到 Azure 工作区。
配置 Log Analytics 代理以读取 Apache HTTP 服务器日志文件。

1. 按照 https://httpd.apache.org/docs/2.4/logs.html 提供的说明设置 Apache HTTP 服务器中的日志文件位置。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”，然后选择“Apache HTTP 服务器(预览版)”。

1. 选择“打开连接器页面”。

1. 按照 Apache HTTP 服务器页面上的说明进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会显示在 ApacheHTTPServer_CL 下的 Log Analytics 中。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解如何将 Apache HTTP 服务器连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。