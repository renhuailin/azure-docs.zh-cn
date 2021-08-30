---
title: 管理 Microsoft Azure 备份服务器 (MABS) 中的遥测设置
description: 本文介绍如何在 MABS 中管理遥测设置。
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: 353aec2b3bf03c7ce06d19db34a6b81f91d2ee12
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722643"
---
# <a name="manage-telemetry-settings"></a>管理遥测设置

>[!NOTE]
>此功能适用于 MABS V3 UR2 及更高版本。

本文介绍如何管理 Microsoft Azure 备份服务器 (MABS) 中的遥测（诊断和实用程序数据）设置。

默认情况下，MABS 向 Microsoft 发送诊断和连接数据。 Microsoft 利用此数据保证并改进 Microsoft 产品和服务的质量、安全性和完整性。

管理员可随时禁用此功能。 有关所收集数据的详细信息，请参阅[以下部分](#telemetry-data-collected)。

## <a name="turn-onoff-telemetry-from-console"></a>从控制台启用/禁用遥测

1. 在 Microsoft Azure 备份服务器控制台中，转到“管理”并单击顶部窗格中的“选项” 。
1. 在“选项”对话框中，选择“诊断和使用情况数据设置”。

    ![控制台遥测选项](./media/telemetry/telemetry-options.png)

1. 从显示的选项中选择诊断和使用情况数据共享首选项，然后单击“确定”。

    >[!NOTE]
    >我们建议在选择选项之前，先阅读[隐私声明](https://privacy.microsoft.com/privacystatement)。
    >- 若要启用遥测，请选择“是，我愿意向 Microsoft 发送数据”。
    >- 若要禁用遥测，请选择“不，我不愿意向 Microsoft 发送数据”。

## <a name="telemetry-data-collected"></a>收集的遥测数据

| 数据相关对象 | 收集的数据* |
| --- | --- |
| **安装** | 已安装的 MABS 版本。 <br/><br/>已安装的 MABS 更新汇总版本。 <br/><br/> 唯一计算机标识符。 <br/><br/> 安装了 MABS 的操作系统。 <br/><br/> 唯一的云订阅标识符。<br/><br/> MARS 代理版本。<br/><br/> 是否启用了分层存储。 <br/><br/> 使用的存储大小。 |
| **受保护的工作负荷** | 工作负荷唯一标识符。 <br/><br/>要备份的工作负荷的大小。 <br/><br/>工作负荷类型及其版本号。 <br/><br/>工作负载目前是否受 MABS 保护。 <br/><br/>保护工作负荷的保护组的唯一标识符。<br/><br/> 将工作负荷备份到的位置 - 备份到磁盘/磁带或云。|
| **作业** | 备份/还原作业的状态。 <br/><br/> 备份/还原的数据的大小。 <br/><br/>失败消息（如果备份/还原作业失败）。<br/><br/> 还原作业花费的时间。<br/><br/>为其运行还原作业的工作负荷的详细信息。 |
| **遥测更改状态** | 遥测设置的状态更改详细信息，是已启用还是已禁用以及时间。 |
| MABS 控制台故障错误 | DPM 控制台故障时的错误详细信息。|

## <a name="next-steps"></a>后续步骤

[保护工作负荷](./back-up-hyper-v-virtual-machines-mabs.md)