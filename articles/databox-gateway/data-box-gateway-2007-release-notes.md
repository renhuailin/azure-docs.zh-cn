---
title: Azure Stack Edge 和 Azure Data Box Gateway 2007 发行说明 | Microsoft Docs
description: 介绍运行 2007 版本的 Azure Stack Edge 和 Data Box Gateway 的关键未结问题和解决方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581614"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Azure Stack Edge 和 Azure Data Box Gateway 2007 发行说明

以下发行说明列出了 Azure Stack Edge 和 Data Box Gateway 的 2007 版本的关键未解决问题和已解决问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 Azure Stack Edge/Data Box Gateway 之前，请仔细查看发行说明中包含的信息。

此版本对应于以下软件版本：

- **Azure Stack Edge 2007 (1.6.1280.1667)** - KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** - KB 4566550

> [!NOTE]
> 2007 版更新只能应用于运行该软件正式发布版 (GA) 或更高版本的所有设备。

## <a name="whats-new"></a>新增功能

此版本包含以下 bug 修复：

- **上传问题** - 此版本解决了由于失败而重启上传可能会降低上传完成的速率这一上传问题。 上传的数据集主要由相对于可用带宽较大的文件组成时，尤其是（但不限于）带宽限制有效时，可能会出现此问题。 此更改可确保在重启给定文件的上传之前，有足够的机会完成上传。

此版本还包含以下更新：

- 更新了 Windows VHD 的基础映像。
- 包括截至 2020 年 5 月发布的所有 Windows 累积更新和 .NET Framework 累积更新。
- 此版本支持 Azure Stack Edge 设备上的 IoT Edge 1.0.9.3。

## <a name="known-issues-in-this-release"></a>本版本中的已知问题

此版本的发行说明中，没有提出任何新的问题。 在发行说明中提到的所有问题都是从以前的版本带过来的。 若要查看已知问题的列表，请参阅[正式发布版中的已知问题](data-box-gateway-release-notes.md#known-issues-in-ga-release)。

## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)
- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
