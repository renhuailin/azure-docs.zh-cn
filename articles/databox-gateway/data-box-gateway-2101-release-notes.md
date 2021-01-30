---
title: Azure Data Box Gateway 2101 发行说明 |Microsoft Docs
description: 描述运行2101版本的 Azure Data Box Gateway 的关键打开问题和解决方法。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072583"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Azure Data Box Gateway 2101 发行说明

以下发行说明说明了2101版 Azure Data Box Gateway 的关键的待解决问题和解决的问题。

发行说明会持续更新。 如果发现了需要解决方法的关键问题，则会将其添加到中。 在部署 Azure Data Box Gateway 之前，请仔细查看发行说明中的信息。  

此版本对应于软件版本：

- **Data Box Gateway 2101 (1.6.1475.2528)** -KB 4603462

> [!NOTE]
> 更新2101只能应用于) 软件或更高版本的 (GA 版本的所有正在运行的设备。

## <a name="whats-new"></a>新增功能

此版本包含以下 bug 修复：

- **上传问题** -此版本修复了上载问题，因为失败的原因可能会降低上载完成的速度。 如果上传的数据集主要包含大小较大（相对于可用带宽）的文件，尤其是在带宽限制处于活动状态时，则会出现此问题。 此更改可确保在重新启动给定文件的上传之前完成上载完成的机会。

此版本还包含以下更新：

- 所有累积 Windows 更新和 .NET framework 更新均已发布到10月2020。
- 每个软件更新将保留 Azure Data Box Gateway 的静态 IP 地址。

## <a name="known-issues-in-this-release"></a>本版本中的已知问题

对于此版本，不会记录任何新问题。 所有已记录版本的问题已从以前的版本中继续执行。 若要查看已知问题的列表，请参阅 [GA 版本中的已知问题](data-box-gateway-release-notes.md#known-issues-in-ga-release)。

## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
