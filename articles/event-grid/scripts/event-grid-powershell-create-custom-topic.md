---
title: Azure PowerShell 脚本示例 - 创建自定义主题 | Microsoft Docs
description: 本文提供了一个示例 Azure PowerShell 脚本，演示如何创建事件网格自定义主题。
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: tomfitz
ms.openlocfilehash: 915b8e13adaa440063f5db62b72fa39e1035d576
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460773"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>使用 PowerShell 创建事件网格自定义主题

此脚本创建事件网格自定义主题。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建自定义主题。 表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzEventGridTopic](/powershell/module/az.eventgrid/new-azeventgridtopic) | 创建事件网格自定义主题。 |

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/get-started-azureps)。
