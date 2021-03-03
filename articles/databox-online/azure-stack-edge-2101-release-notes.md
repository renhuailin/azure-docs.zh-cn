---
title: Azure Stack Edge Pro 与 FPGA 2101 发行说明 |Microsoft Docs
description: 描述 Azure Stack Edge 2101 版本的关键打开问题和解决方法。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 5c95da8d6ee88786ff983b4963c1cb96394886cf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727543"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Azure Stack Edge Pro 与 FPGA 2101 发行说明

以下发行说明通过内置的现场可编程门阵列 (FPGA) ，识别2101版 Azure Stack Edge Pro 的严重打开问题和解决的问题。

发行说明会持续更新。 如果发现了需要解决方法的关键问题，则会将其添加到中。 在部署 Azure Stack Edge 设备之前，请仔细查看发行说明中的信息。  

此版本对应于软件版本：

- **Azure Stack Edge 2101 (1.6.1475.2528)** -KB 4599267

> [!NOTE]
> 更新2101只能应用于) 软件或更高版本的 (GA 版本中运行的设备。

## <a name="whats-new"></a>新增功能

此版本包含以下 bug 修复：

- **上传问题** -此版本修复了上传问题，导致失败的上载重启可能会降低上载完成的速度。 如果上传的数据集主要包含相对于可用带宽大的文件（尤其是，但带宽限制处于活动状态时），则会出现此问题。 此更改可确保在重新启动给定文件的上传之前完成上载完成的机会。

此版本还包含以下更新：

- 所有累积 Windows 更新和 .NET framework 更新均已发布到10月2020。
- 在出厂安装期间，基板管理控制器 (BMC) 固件版本从3.32.32.32 升级到3.36.36.36，以解决与较新的 Dell 电源设备的不兼容问题。
- 此版本支持 Azure Stack 边缘设备上的 IoT Edge 1.0.9.3。

## <a name="known-issues-in-this-release"></a>本版本中的已知问题

对于此版本，不会记录任何新问题。 所有已记录版本的问题已从以前的版本中继续执行。 若要查看已知问题的列表，请参阅 [GA 版本中的已知问题](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release)。

## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)