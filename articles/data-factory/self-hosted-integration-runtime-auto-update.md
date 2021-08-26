---
title: 自承载集成运行时自动更新和过期通知
description: 了解有关自承载集成运行时自动更新和过期通知的信息
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 06/16/2021
ms.openlocfilehash: f59948204af76ce5e2d940c2910601b848bb4605
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637780"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>自承载集成运行时自动更新和过期通知

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文将介绍如何让自承载集成运行时自动更新到最新版本，以及 ADF 如何管理自承载集成运行时的版本。

## <a name="self-hosted-integration-runtime-auto-update"></a>自承载集成运行时自动更新
通常，在本地计算机或 Azure VM 中安装自承载集成运行时时，可以通过两个选项来管理自承载集成运行时的版本：自动更新或手动维护。 通常，ADF 每月都会发布两个新版本的自承载集成运行时，其中包括新功能版、bug 修复或增强功能。 因此，建议用户更新到较新版本，以获得最新功能和增强功能。

最方便的方法是在创建或编辑自承载集成运行时时启用自动更新， 自承载集成运行时将自动更新到较新版本。 还可以根据需要在最合适的时间段计划更新。

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update.png" alt-text="启用自动更新":::

可以在自承载集成运行时客户端中检查上次更新日期/时间。

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update-2.png" alt-text="检查更新时间的屏幕截图":::

你可以使用此 [PowerShell 命令](/powershell/module/az.datafactory/get-azdatafactoryv2integrationruntime?view=azps-6.1.0&preserve-view=true#example-5--get-self-hosted-integration-runtime-with-detail-status)来获取自动更新版本。 

> [!NOTE]
> 如果你有多个自承载集成运行时节点，则自动更新期间不会停机。 自动更新先在一个节点中进行，与此同时，其他节点可以处理任务。 当第一个节点完成更新时，它会接管剩余的任务，而其他节点可以更新。 如果你只有一个自承载集成运行时，则自动更新期间会停机一段时间。

## <a name="auto-update-version-vs-latest-version"></a>自动更新版本与最新版本
为了确保自承载集成运行时的稳定性，尽管我们会发布两个版本，但每月只会推送一个版本。 因此，有时你会发现，自动更新版本就是实际最新版本的前一版本。 如果要获取最新版本，可以访问[下载中心](https://www.microsoft.com/download/details.aspx?id=39717)。

如果当前版本较旧，ADF 门户中的自承载集成运行时“自动更新”页将显示较新版本。 当自承载集成运行时联机时，此版本将成为自动更新版本，并且会按计划的时间自动更新自承载集成运行时。 但是，如果自承载集成运行时已脱机，则该页只会显示最新版本。

## <a name="self-hosted-integration-runtime-expire-notification"></a>自承载集成运行时过期通知
如果要手动控制自承载集成运行时的版本，则可以禁用自动更新设置并进行手动安装。 自承载集成运行时的每个版本都将在一年后过期。 ADF 门户和自承载集成运行时客户端会在过期前 90 天内显示过期消息。

## <a name="next-steps"></a>后续步骤

- 查看 [Azure 数据工厂中的集成运行时概念](./concepts-integration-runtime.md)。

- 了解如何[在 Azure 门户中创建自承载集成运行时](./create-self-hosted-integration-runtime.md)。
