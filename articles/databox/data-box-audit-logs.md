---
title: Azure Data Box 与 Azure Data Box Heavy 事件的审核日志 | Microsoft Docs
description: 描述在 Azure Data Box 的各个阶段收集的 Data Box 的完整审核日志，并 Azure Data Box Heavy 订单。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "86209304"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box 和 Azure Data Box Heavy 的审核日志

日志是对一段时间内发生的离散事件的记录，日志不可改变而且有时间戳。 日志包含设备中的诊断、审核和安全信息。  

Data Box 或 Data Box Heavy 订单会在操作过程中会经历以下步骤：订购、设置、数据复制、寄回、上传到 Azure、验证和数据擦除。 对于上述每个步骤，将审核并记录所有事件。

本文包含有关 Data Box 审核日志的信息，其中包括日志类型和收集的信息以及日志的位置。 

本文中的信息同时适用于 Data Box 和 Data Box Heavy。 在后续部分，有关 Data Box 的任何参考信息也适用于 Data Box Heavy。 本文不介绍从 Azure 中运行的 Data Box 服务收集的日志。 


## <a name="about-audit-logs"></a>关于审核日志 

在 Data Box 上，将收集以下日志：

- 系统日志 - Data Box 作为基于 Windows 的设备，将记录所有硬件、软件和系统事件。 系统审核日志中收集并报告了一组这些事件。 

- 安全 - Data Box 成为基于 Windows 的设备，将记录所有安全事件。 在安全审核日志中收集和报告一组此类事件。 

- 应用程序 - 这些日志仅特定于 Data Box。 这些日志包含设备上生成的所有事件，以响应正在运行的 Data Box 服务。

下节介绍了其中每一个步骤。

### <a name="system-logs"></a>系统日志

以下系统日志事件 ID 将作为系统审核日志收集到 Data Box：

|事件提供程序名称     |收集的事件 ID   |事件说明   |
|-------------------|----------|----------------|
|Microsoft-Windows-Kernel-General|12  |OS 重新启动时的 UTC 时间。   |
|                                |13  |OS 关闭时的 UTC 时间。 |
|    |                              |
|Microsoft-Windows-kernel-Power  |41  |系统在无错关闭的情况下重新启动。| 
|    |                              |
|Microsoft-Windows-BitLocker-Driver|全部|    |

### <a name="security-logs"></a>安全日志

以下安全日志事件 ID 将作为安全审核日志收集到 Data Box：

|事件提供程序名称                   |收集的事件 ID    |事件说明       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-Security-Auditing   |4624        |成功登录。 |
|                                      |4625        |帐户登录失败。 用户名未知或密码错误。 |
|                                     

### <a name="application-logs"></a>应用程序日志

以下应用程序日志事件 ID 将作为 Data Box 上包审核日志的一部分收集。     

- Microsoft-Azure-DataBox-OOBE-Auditing - 包含本地 UI 中发生的事件。 
- Microsoft-Azure-DataBox-Reprovision-Audit - 包含与 Data Box 设备的重新预配相关的事件。 当通过本地 UI 重置设备时，将进行 Data Box 的重新预配。 如果要擦除已复制的数据，请选择此选项，方法是删除现有共享，然后在重新预配或设备重置的过程中重新创建共享。
- Microsoft-Azure-DataBox-HcsMgmt-Audit - 包含的事件仅与“准备交付”步骤相关，该步骤是设备运回 Azure 数据中心之前进行。 
- Microsoft-Azure-DataBox-IfxAudit - 包含由产品的不同实体记录的关于作业的消息，这些消息指示有关某些流中发生的情况的详细信息。

下表汇总了各种事件提供程序以及在每种情况下收集的相应事件 ID。

|事件提供程序名称    |事件 ID    | 备注 |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-DataBox-OOBE-Auditing |4624        |成功登录。|
|                                      |4625        |帐户登录失败。 用户名未知或密码错误。|
|                                     |4634        |注销事件。|
|                                   |  | |
|Microsoft-Azure-DataBox-Reprovision-Audit    |65001       |重新预配成功事件。|
|                                                  |65002       |重新预配失败事件。|
|                                                  |                 |         |
|Microsoft-Azure-DataBox-HcsMgmt-Audit        |65003       |准备交付状态事件     NotStarted,     InProgress,     Failed,     Canceled,     Succeeded,     ScanCompletedWithIssues,     SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-DataBox-IfxAudit    |全部 |所有事件均通过审核日志 API 以代码形式记录 |

下面是 Instrumentation Framework (IFX) 审核日志的示例：

|     任务/作业/API                              |     记录的事件                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     清理                                   |     将记录与清除作业的开始、完成或失败相关的事件。 |                                              
|     为客户发货准备设备    |     将记录用于准备设备发货的作业开始、完成或失败相关事件。 |
|     预配                                 |     将记录与设备预配作业的开始、完成或失败相关的事件。|
|     审核包作业                       |     将记录与创建保管链日志链相关的审核包作业的开始、完成或失败事件。|
|     磁盘覆盖                          |     将记录磁盘覆盖失败的情况。|
|     启用或禁用远程 PowerShell     |     将记录与启用或禁用设备上的远程 PowerShell 相关的事件。 |
|     获取安装阶段详细信息               |     将在 Azure 数据中心内分阶段记录与设备上安装软件相关的事件。|
|     解锁或锁定 BitLocker 卷           |     记录事件，以指示 basevolume 和 Hcsdata 卷的 BitLocker 状态。|
|     清理磁盘                              |     记录与无法擦除的物理磁盘故障相关的事件，以及设备上的所有物理磁盘均已成功擦除的事件。 |
|     启用或禁用本地用户               |     记录与 Storsimpleadmin 身份和 PodSupportAdminUser 的本地用户帐户的启用或禁用相关的事件。| 
|     密码重置                          |     记录与本地 Storsimpleadmin 身份的密码重置成功或失败相关的事件。 |


除了 IFX 审核日志之外，还会为 Data Box 收集保管链审核日志。 这些日志无法实时查看，只能在作业完成后查看，并且数据将从 Data Box 磁盘中删除。 这些日志包含 IFX 审核日志中包含的信息的子集。

有关保管链审核日志的详细信息，请参阅[在数据擦除后获取保管链日志](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure)。

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>访问审核日志

这些日志存储在 Azure 中，不能直接访问。 如果需要访问这些日志，请提交支持票证。 有关详细信息，请参阅[联系 Microsoft 支持部门](data-box-disk-contact-microsoft-support.md)。 

提交支持票证后，Microsoft 将下载这些日志并为你提供这些日志的访问权限。


## <a name="next-steps"></a>后续步骤

- 了解如何[排查 Data Box 和 Data Box Heavy 上的问题](data-box-troubleshoot.md)。
