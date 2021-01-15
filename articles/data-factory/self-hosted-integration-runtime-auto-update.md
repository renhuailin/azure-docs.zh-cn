---
title: 自承载集成运行时自动更新和过期通知
description: 了解自承载集成运行时自动更新和过期通知
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
manager: shwang
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 8c51839e760dcca32bfc0d150c5e4d10767d95a4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222674"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>自承载集成运行时自动更新和过期通知

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍如何让自承载集成运行时自动更新到最新版本，以及 ADF 如何管理自承载集成运行时的版本。

## <a name="self-hosted-integration-runtime-auto-update"></a>自承载 Integration Runtime 自动更新
通常，当你在本地计算机或 Azure VM 中安装自承载集成运行时，有两个选项可用于管理自承载集成运行时的版本：自动更新或手动维护。 通常情况下，ADF 每个月都会发布自承载集成运行时的两个新版本，其中包括新功能版本、bug 修复或增强功能。 因此，建议用户更新到最新版本，以获取最新的功能和增强功能。

最简单的方法是在创建或编辑自承载集成运行时时启用自动更新。 然后，它将自动更新到最新版本。 你还可以根据需要在最适当的时间段计划更新。

![启用自动更新](media/create-self-hosted-integration-runtime/shir-auto-update.png)

你可以在自承载集成运行时客户端中检查上次更新日期时间。

![检查更新时间的屏幕截图](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> 为了确保自承载集成运行时的稳定性，尽管我们发布了两个版本，但我们每个月仅自动更新一次。 因此，有时会发现自动更新版本是实际最新版本的以前版本。 若要获取最新版本，可以转到 [下载中心](https://www.microsoft.com/download/details.aspx?id=39717)。

## <a name="self-hosted-integration-runtime-expire-notification"></a>自承载 Integration Runtime 终止通知
如果要手动控制自承载集成运行时的版本，可以禁用自动更新的设置，并手动安装它。 自承载集成运行时的每个版本将在一年后过期。 过期消息显示在 ADF 门户和自承载集成运行时客户端 **90 天** 内过期之前。

## <a name="next-steps"></a>后续步骤

- 查看 [Azure 数据工厂中的集成运行时概念](./concepts-integration-runtime.md)。

- 了解如何[在 Azure 门户中创建自承载集成运行时](./create-self-hosted-integration-runtime.md)。
