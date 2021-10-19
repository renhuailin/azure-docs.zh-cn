---
title: 利用适用于 Azure 通信服务的 Azure 顾问
titleSuffix: An Azure Communication Services concept document
description: 了解有关适用于 Azure 通信服务的 Azure 顾问产品/服务。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 3c3b995cdf4b27056b0714ae6f9f8bc29fad6302
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713104"
---
# <a name="azure-advisor-for-azure-communication-services"></a>适用于 Azure 通信服务的 Azure 顾问

[Azure 顾问](../../advisor/advisor-overview.md)是个性化的云顾问程序，可帮助遵循最佳做法来优化 Azure 部署。 Azure 通信服务已加入到 Azure 顾问，并将发布有关通信资源优化方法的建议。 可以在 [Azure 门户](https://portal.azure.com)的[顾问边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview)中查看这些建议。 这些建议存储在 [Azure 活动日志](../../azure-monitor/essentials/platform-logs-overview.md)中，可以通过 [ARM 模板](../../advisor/advisor-alerts-arm.md)或[门户](../../advisor/advisor-alerts-portal.md)为建议配置警报。 

## <a name="install-the-latest-sdks"></a>安装最新的 SDK

为确保使用所有最新的修补程序和更新，建议始终安装最新的可用 SDK。 如果所使用的 SDK 有更新的版本，则“性能”类别中会显示建议更新到最新的 SDK。

![显示更新聊天 SDK 的建议的 Azure 顾问示例。](./media/advisor-chat-sdk-update-example.png)

此功能支持以下 SDK 及这些 SDK 支持的所有语言。 可详细了解可用的 [SDK 选项](./sdk-options.md)。

* 通话（客户端）
* 聊天
* SMS
* 标识
* 电话号码
* 管理
* Network Traversal
* 通话自动化

## <a name="next-steps"></a>后续步骤

你可能会对下列文档感兴趣：

- [日志记录和诊断](./logging-and-diagnostics.md)
- [度量值](./metrics.md)
