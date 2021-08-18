---
title: Azure 视频分析器媒体版（之前称为视频索引器）故障转移和灾难恢复
titleSuffix: Azure Video Analyzer for Media
description: 了解在发生区域性数据中心故障或灾难时，如何故障转移到次要 Azure 视频分析器媒体版（之前称为视频索引器）帐户。
services: azure-video-analyzer
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.workload: ''
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: a7361f75a9327a6fbc88132e67e87f9be7ed5f84
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112118835"
---
# <a name="video-analyzer-for-media-failover-and-disaster-recovery"></a>视频分析器媒体版故障转移和灾难恢复

如果区域性数据中心出现中断或故障，Azure 视频分析器媒体版（之前称为视频索引器）不会提供服务的即时故障转移。 本文介绍如何配置环境进行故障转移，以确保在灾难发生时，最大程度地提高应用可用性及缩短恢复时间。

我们建议跨区域对配置业务连续性和灾难恢复 (BCDR)，以便从 Azure 的隔离和可用性策略中受益。 有关详细信息，请参阅 [Azure 配对区域](../../best-practices-availability-paired-regions.md)。

## <a name="prerequisites"></a>先决条件

Azure 订阅。 如果尚无 Azure 订阅，请注册 [Azure 免费试用](https://azure.microsoft.com/free/)帐户。

## <a name="failover-to-a-secondary-account"></a>故障转移到辅助帐户

若要实现 BCDR，你需要拥有两个视频分析器媒体版帐户来处理冗余。

1. 创建两个连接至 Azure 的视频分析器媒体版帐户（参阅[创建视频分析器媒体版帐户](connect-to-azure.md)）。 为主要区域创建一个帐户，并为配对的 Azure 区域创建另一个帐户。
1. 如果主要区域发生故障，请使用辅助帐户切换到索引编制。

> [!TIP]
> 你可以按照[创建有关服务通知的活动日志警报](../../service-health/alerts-activity-log-service-notifications-portal.md)通过设置服务运行状况通知的活动日志警报来自动执行 BCDR。

有关使用多个租户的信息，请参阅[管理多个租户](manage-multiple-tenants.md)。 若要实现 BCDR，请选择以下两个选项之一：[每个租户都有视频分析器媒体版帐户](./manage-multiple-tenants.md#video-analyzer-for-media-account-per-tenant)或[每个租户都有 Azure 订阅](./manage-multiple-tenants.md#azure-subscription-per-tenant)。

## <a name="next-steps"></a>后续步骤

[管理连接至 Azure 的视频分析器媒体版帐户](manage-account-connected-to-azure.md)。
