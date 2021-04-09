---
title: 自承载集成运行时自动更新和过期通知
description: 了解有关自承载集成运行时自动更新和过期通知的信息
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376252"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>自承载集成运行时自动更新和过期通知

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文将介绍如何让自承载集成运行时自动更新到最新版本，以及 ADF 如何管理自承载集成运行时的版本。

## <a name="self-hosted-integration-runtime-auto-update"></a>自承载集成运行时自动更新
通常，在本地计算机或 Azure VM 中安装自承载集成运行时时，可以通过两个选项来管理自承载集成运行时的版本：自动更新或手动维护。 通常，ADF 每月都会发布两个新版本的自承载集成运行时，其中包括新功能版、bug 修复或增强功能。 因此，建议用户更新到最新版本，以获得最新功能和增强功能。

最方便的方法是在创建或编辑自承载集成运行时时启用自动更新， 随后它将自动更新为最新版本。 还可以根据需要在最合适的时间段计划更新。

![启用自动更新](media/create-self-hosted-integration-runtime/shir-auto-update.png)

可以在自承载集成运行时客户端中检查上次更新日期/时间。

![检查更新时间的屏幕截图](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> 为了确保自承载集成运行时的稳定性，尽管我们发布了两个版本，但每个月仅自动更新一次。 因此，有时你会发现自动更新版本是实际最新版本的以前版本。 如果要获取最新版本，可以访问[下载中心](https://www.microsoft.com/download/details.aspx?id=39717)。

## <a name="self-hosted-integration-runtime-expire-notification"></a>自承载集成运行时过期通知
如果要手动控制自承载集成运行时的版本，则可以禁用自动更新设置并进行手动安装。 自承载集成运行时的每个版本都将在一年后过期。 ADF 门户和自承载集成运行时客户端会在过期前 90 天内显示过期消息。

## <a name="next-steps"></a>后续步骤

- 查看 [Azure 数据工厂中的集成运行时概念](./concepts-integration-runtime.md)。

- 了解如何[在 Azure 门户中创建自承载集成运行时](./create-self-hosted-integration-runtime.md)。
