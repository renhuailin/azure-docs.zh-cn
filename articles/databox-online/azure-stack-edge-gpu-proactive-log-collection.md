---
title: 了解 Azure Stack Edge Pro 设备上的主动日志收集
description: 描述如何在 Azure Stack Edge Pro 设备上完成主动日志收集以及如何禁用该收集。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: alkohli
ms.openlocfilehash: ef54d3a252baa8d84588481b2a73d1b365bb8c13
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721407"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Azure Stack Edge 设备上的主动日志收集

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

主动日志收集收集 Azure Stack Edge 设备上的系统运行状况指示器，帮助你高效地排查任何设备问题。 默认情况下启用主动日志收集。 本文介绍了所记录的内容、Microsoft 如何处理数据，以及如何禁用或启用主动日志收集。

## <a name="about-proactive-log-collection"></a>关于主动日志收集

Microsoft 客户支持和工程团队使用 Azure Stack Edge 设备中的系统日志来高效识别和修复操作过程中可能出现的问题。 “主动日志收集”是一种针对客户的 Azure Stack Edge 设备检测到问题/事件提醒 Microsoft 的方法。 有关跟踪的事件，请参阅[主动日志收集指示器](#proactive-log-collection-indicators)。 与此问题相关的支持日志会自动上传到 Microsoft 管理和控制的 Azure 存储帐户。 Microsoft 支持部门和 Microsoft 工程师会查看这些支持日志，确定解决客户问题的最佳操作过程。

> [!NOTE]
> 这些日志仅用于调试目的，并在出现问题时向客户提供支持。


## <a name="enabling-proactive-log-collection"></a>启用主动日志收集

默认情况下启用主动日志收集。 尝试通过本地 UI 激活设备时，可以禁用主动日志收集。 

1. 在设备的本地 Web UI 中，转到“开始使用”页。

2. 在“激活”磁贴中选择“激活” 。 

    ![本地 Web UI“云详细信息”第 1 页](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. 在“激活”窗格中：

   1. 输入你在[为 Azure Stack Edge Pro R 获取激活密钥](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)中获得的激活密钥。

      激活后，默认情况下启用主动日志收集，这样 Microsoft 可以收集基于设备运行状态的日志。 这些日志会上传到 Azure 存储帐户。 

      可以禁用主动日志收集以使 Microsoft 停止收集日志。

   1. 如果要在设备上禁用主动日志收集，请选择“禁用”。

   1. 选择“激活”  。

   ![本地 Web UI“云详细信息”页 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)

## <a name="proactive-log-collection-indicators"></a>主动日志收集指示器

启用了主动日志收集后，在设备上检测到以下事件之一时，系统会自动上传日志：  


|Alert/Error/Condition  |说明  |
|---------|---------|
|AcsUnhealthyCondition     |Azure 一致服务未正常运行。         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge 代理未运行。         |
|UpdateInstallFailedEvent | 无法安装此更新。        |

 
Microsoft 将继续向前面的列表中添加新的事件。 新事件不需要额外的许可。 请参阅此页面，了解最新的主动日志收集指示器。    
 

## <a name="other-log-collection-methods"></a>其他日志收集方法

除了主动日志收集（用于收集与检测到的特定问题相关的特定日志）外，其他日志收集也可以针对系统运行状况和行为提供更深入的了解。 通常，这些其他日志可在支持请求期间收集，也可由 Microsoft 根据设备的遥测数据触发。

## <a name="handling-data"></a>处理数据

启用主动日志收集时，客户同意 Microsoft 从 Azure Stack Edge 设备收集日志，如本文所述。 客户还确认并同意，这些日志可以上传并保留在由 Microsoft 管理和控制的 Azure 存储帐户中。

Microsoft 对数据的使用仅限于对系统运行状况和问题进行故障排除。 未经客户同意，数据不得用于市场营销、广告或任何其他商业目的。 

Microsoft 收集的数据会根据我们的标准隐私做法进行处理。 如果客户决定撤销其主动日志收集许可，则在撤销之前同意收集的任何数据都不会受到影响。

## <a name="next-steps"></a>后续步骤

了解如何[收集支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)。