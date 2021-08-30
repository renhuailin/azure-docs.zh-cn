---
title: include 文件
description: include 文件
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5c3a653d66ddfb00833f2b90d61f08a51697fd46
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113132014"
---
> [!NOTE]
> Web 应用可在进入非活动状态 20 分钟后超时，只有向实际 Web 应用发出请求才会重置计时器。 在 Azure 门户中查看应用的配置或向高级工具站点 (`https://<app_name>.scm.azurewebsites.net`) 发出请求不会重置计时器。 如果将 Web 应用设置为持续运行、按计划运行或使用事件驱动的触发器，请在 Web 应用的 Azure“配置”页面上启用“始终可用”设置 。 “始终可用”设置有助于确保这些类型的 Web 作业可靠运行。 此功能仅在基本、标准和高级[定价层](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)中提供。
