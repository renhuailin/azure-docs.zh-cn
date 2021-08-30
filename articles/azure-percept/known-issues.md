---
title: Azure Percept 已知问题
description: 详细了解 Azure Percept 已知问题及其解决方法
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: bf1e6f1297e73811e4ce7044319b13dc4c5b149f
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295864"
---
# <a name="azure-percept-known-issues"></a>Azure Percept 已知问题

以下是产品团队所知道的 Azure Percept DK、Azure Percept Audio 或 Azure Percept Studio 的问题。 尽可能提供解决方法和故障排除步骤。 如果被这些问题所困扰，你可以在 [Microsoft Q&A](/answers/topics/azure-percept.html) 上发布问题，或者在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)中提交客户支持请求。 

|区域|症状|问题描述|解决方法|
|-------|---------|---------|---------|
| Azure Percept DK | 无法在 Azure Percept Studio 中部署示例和演示模型 | 有时 azureeyemodule 或 azureearspeechmodule 模块会停止运行。 edgeAgent 日志显示“符号链接级别过多”错误。 | [通过 USB 更新设备](./how-to-update-via-usb.md)来重置设备 |
| 本地化 | 非英语用户可能会看到部分 Azure Percept DK 安装体验显示英语文本。 | Azure Percept DK 安装体验未完全本地化。 | 修复计划于 2021 年 7 月进行  |
| Azure Percept DK | 在 Mac 上进行安装体验时，安装体验可能会在连接 Wi-Fi 后突然关闭。 | 在 Mac 上完成安装体验时，首先会打开窗口而不是打开 Web 浏览器。 连接从设备接入点切换到 Wi-Fi 后，窗口将关闭。 | 打开 Web 浏览器并转到 https://10.1.1.1 ，并在此完成安装过程。 |
| Azure Percept DK | 开发工具包正在运行自定义模型，在重新启动开发工具包后，它会运行默认示例模型。 | 自定义模型的模块孪生容器不会在设备重启时保留。 重启后，必须重新生成自定义模块的模块孪生，这可能需要 5 分钟或更长时间。 开发工具包将运行默认模型，直到该过程完成。 | 重启后，必须等到自定义模块孪生重新创建。 |