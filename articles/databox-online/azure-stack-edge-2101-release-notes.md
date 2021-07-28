---
title: Azure Stack Edge Pro FPGA 2101 发行说明 | Microsoft Docs
description: 介绍 Azure Stack Edge Pro FPGA 2101 版的关键未结问题和解决方案。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 3c8882706bdbb6719dbc2550df350096a4970fd8
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110461215"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Azure Stack Edge Pro with FPGA 2101 发行说明

以下发行说明列出了使用内置现场可编程门阵列 (FPGA) 的 Azure Stack Edge Pro FPGA 2101 版的关键未结问题和已解决问题。

发行说明会持续更新。 如果发现了需要提供解决方法的重要问题，则会添加到其中。 在部署 Azure Stack Edge Pro 之前，请仔细查看发行说明中的信息。  

此版本对应软件版本：

- **Azure Stack Edge 2101 (1.6.1475.2528)** - KB 4599267

> [!NOTE]
> 2101 版更新内容只能应用于运行该软件正式发布版 (GA) 或更高版本的设备。

## <a name="whats-new"></a>新变化

此版本包含以下 bug 修复：

- **上传问题** - 此版本修复了上传问题，即由于失败而重启上传可能会降低上传完成的速率。 上传主要由相对于可用带宽较大的文件组成的数据集时，尤其是（但不限于）带宽限制处于活动状态时，可能会出现此问题。 此更改可确保在重启给定文件的上传之前，有足够的机会完成上传。

此版本还包含以下更新：

- 所有累积 Windows 更新和 .NET Framework 更新均已在 2020 年 10 月发布。
- 在出厂安装期间，已将基板管理控制器 (BMC) 固件版本从 3.32.32.32 升级到 3.36.36.36，以解决与更高版本 Dell 电源设备不兼容的问题。
- 此版本支持 Azure Stack Edge 设备上的 IoT Edge 1.0.9.3。

## <a name="known-issues-in-this-release"></a>本版本中的已知问题

此版本的发行说明中，没有提出任何新的问题。 在发行说明中提到的所有问题都是从以前的版本带过来的。 若要查看已知问题的列表，请参阅[正式发布版中的已知问题](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release)。

## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)