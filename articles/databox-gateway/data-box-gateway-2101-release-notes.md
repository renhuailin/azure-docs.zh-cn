---
title: Azure Data Box Gateway 2101 发行说明 | Microsoft Docs
description: 介绍运行 Azure Data Box Gateway 2101 版本的严重待解决问题和解决方法。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99072583"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Azure Data Box Gateway 2101 发行说明

以下发行说明说明了 Azure Data Box Gateway 2101 版本的严重待解决问题和已解决的问题。

发行说明会持续更新。 如果发现了需要提供解决方法的重要问题，则会添加到其中。 在部署 Azure Data Box Gateway 之前，请仔细查看本发行说明中的信息。  

此版本对应于以下软件版本：

- Data Box Gateway 2101 (1.6.1475.2528) - KB 4603462

> [!NOTE]
> 2101 版更新内容只能应用于运行该软件正式发布版 (GA) 或更高版本的所有设备。

## <a name="whats-new"></a>新增功能

此版本包含以下 bug 修复：

- 上传问题 - 此版本修复了上传问题，即由于失败而重启上传可能会降低上传完成的速率。 上传主要由较大文件组成的数据集时，可能会出现此问题，文件较大是相对于可用带宽而言，尤其是（但不限于）带宽限制处于活动状态时。 此更改可确保在重启给定文件的上传之前，有足够的机会完成上传。

此版本还包含以下更新：

- 截至 2020 年 10 月发布的所有累积 Windows 更新和 .NET Framework 更新。
- Azure Data Box Gateway 的静态 IP 地址保留在软件更新中。

## <a name="known-issues-in-this-release"></a>本版本中的已知问题

此版本的发行说明中，没有提出任何新的问题。 在发行说明中提到的所有问题都是从以前的版本带过来的。 若要查看已知问题的列表，请参阅[正式发布版中的已知问题](data-box-gateway-release-notes.md#known-issues-in-ga-release)。

## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
