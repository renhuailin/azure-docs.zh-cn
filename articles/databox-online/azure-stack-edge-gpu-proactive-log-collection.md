---
title: 了解 Azure Stack Edge Pro 设备上的主动日志收集
description: 描述如何在 Azure Stack Edge Pro 设备上完成主动日志收集。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466043"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Azure Stack 边缘设备上的主动日志收集

你可以基于系统运行状况指示符在 Azure Stack Edge 设备上启用主动日志收集，以帮助有效地排查任何设备问题。 本文介绍了什么是主动日志收集、如何启用它以及如何在启用主动日志收集时处理数据。
   
本文中的信息适用于 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack 边缘迷你 R 设备。

## <a name="about-proactive-log-collection"></a>关于主动日志收集

Microsoft 客户支持和工程团队使用 Azure Stack Edge 设备中的系统日志来有效识别和修复操作过程中可能出现的问题。 "主动日志收集" 是一种方法，该方法向 Microsoft 发出警报：问题/事件 (查看) 已被客户的 Azure Stack 边缘设备检测到的事件的 "主动日志收集指示器" 部分。 与此问题相关的支持日志会自动上传到由 Microsoft 管理和控制的 Azure 存储帐户。 Microsoft 支持部门和 Microsoft 工程师查看这些支持日志，确定解决客户问题的最佳操作过程。    

> [!NOTE]
> 这些日志仅用于调试目的，并在出现问题时向客户提供支持。 


## <a name="enabling-proactive-log-collection"></a>启用主动日志收集

尝试通过本地 UI 激活设备时，可以启用主动日志收集。 

1. 在设备的本地 Web UI 中，转到“开始使用”页。
2. 在“激活”磁贴中选择“激活” 。 

    ![本地 web UI "云详细信息" 第1页](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. 在 " **激活** " 窗格中：
    1. 输入 [获取 Azure Stack Edge Pro R 的激活密钥中获取](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)的 **激活密钥**。

    1. 你可以启用主动日志收集，让 Microsoft 根据设备的运行状态收集日志。 以这种方式收集的日志会被上传到 Azure 存储帐户。
    
    1. 选择“应用”。 

    ![本地 Web UI“云详细信息”页 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)



## <a name="proactive-log-collection-indicators"></a>主动日志收集指示器

启用了主动日志收集后，在设备上检测到以下事件之一时，会自动上载日志：  


|警报/错误/条件  |说明  |
|---------|---------|
|AcsUnhealthyCondition     |Azure 一致服务处于不正常状态。         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge 代理未在运行。         |
|UpdateInstallFailedEvent | 无法安装更新。        |

 
Microsoft 将继续向前面的列表中添加新的事件。 新事件不需要额外的许可。 请参阅此页面，了解最新的前瞻性指标。    
 

## <a name="other-log-collection-methods"></a>其他日志收集方法

除了收集与检测到的特定问题相关的特定日志以外，还可以使用其他日志集合来更深入地了解系统运行状况和行为。 通常，这些其他日志集合可以在支持请求期间执行，或由 Microsoft 根据设备提供的遥测数据来触发。  

## <a name="handling-data"></a>处理数据

如果客户启用了主动日志收集，则他们同意从 Azure Stack Edge 设备收集日志，如此处所述。 客户还会确认并同意在 Microsoft 管理和控制的 Azure 存储帐户中上传和保留这些日志。

Microsoft 使用数据对系统运行状况和问题进行故障排除。 数据不用于市场营销、广告或任何其他商业目的，无需客户许可。 

Microsoft 收集的数据将根据我们的标准隐私做法进行处理。 如果客户决定撤消其主动预防性日志收集许可，则在吊销之前以同意收集的任何数据都不会受到影响。

## <a name="next-steps"></a>后续步骤

了解如何 [收集支持包](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)。