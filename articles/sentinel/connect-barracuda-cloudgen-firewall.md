---
title: 将 Barracuda CloudGen Firewall 连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Barracuda CloudGen Firewall 连接到 Azure Sentinel。
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 1ff3d5481b02e587d993bb4a5d7abf6aecd22ee8
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254053"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>连接 Barracuda CloudGen 防火墙

利用 Barracuda CloudGen Firewall (CGFW) 连接器，可以轻松地将 Barracuda CGFW 日志与 Azure Sentinel 连接、查看仪表板、创建自定义警报以及改善调查。 这样，用户就可以更深入地了解组织的网络并改善安全操作功能。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>先决条件

- Azure Sentinel 工作区的读取和写入权限。

- 必须将 Barracuda CloudGen Firewall 配置为通过 Syslog 导出日志。

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>将 Azure Sentinel 连接到 Barracuda CloudGen Firewall

1. 在 Azure 门户中，导航到“Azure Sentinel” > “数据连接器”，然后选择“Barracuda CloudGen Firewall”连接器。  

1. 选择“打开连接器页面”。

1. 按照“Barracuda CloudGen Firewall”页面上的说明进行操作。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Barracuda CloudGen Firewall 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。