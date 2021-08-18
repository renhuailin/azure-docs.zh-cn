---
title: 关于警报消息
description: 从“警报”窗口选择警报，以查看详细信息。
ms.date: 3/21/2021
ms.topic: how-to
ms.openlocfilehash: 2fa2b265c7d3983ca6ae2d7507392dd37afabd27
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015425"
---
# <a name="about-alert-messages"></a>关于警报消息

从“警报”窗口选择警报，以查看警报详细信息。 详细信息包含以下信息：

- 警报元数据

- 有关流量、设备和事件的信息

- 设备映射中联网设备的链接

- 由安全分析师和管理员定义的注释

- 有关调查事件的建议

## <a name="alert-metadata"></a>警报元数据

警报详细信息包括以下警报元数据：

  - 警报 ID

  - 触发该警报的策略引擎

  - 触发警报的日期和时间

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="检测到未经授权的 Internet 连接。":::

## <a name="information-about-devices-traffic-and-the-event"></a>有关设备、流量和事件的信息

警报消息提供有关以下方面的信息：

  - 检测到的设备。

  - 在设备之间检测到的流量，例如协议和函数代码。

  - 有关事件含义的见解。

可以在决定如何管理警报事件时使用这些信息。

## <a name="links-to-connected-devices-in-the-device-map"></a>设备映射中联网设备的链接

若要详细了解与检测到的设备相连接的设备，可以在警报中选择设备映像，并在映射中查看连接的设备。

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="RCP 操作失败。":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="设备屏幕截图。":::

映射会筛选到所选设备，以及其他连接到它的设备。 映射还会为在警报中检测到的设备显示“快速属性”对话框。

## <a name="comments-defined-by-security-analysts-and-administrators"></a>由安全分析师和管理员定义的注释 

警报可能会包含预定义注释的列表。 例如，注释可以是要执行的缓解措施的说明，或者是要针对事件进行联系的个人的姓名。

在管理警报事件时，可以选择最能反映事件状态或为调查警报而采取的步骤的注释。

所选注释会保存在警报消息中。 使用注释会增进警报事件调查过程中个人和团队之间的交流。 因此，注释可以加快事件响应速度。

管理员或安全分析师会预定义注释。 所选注释不会转发到转发规则中定义的合作伙伴系统。

在查看警报中的信息后，可以执行各种会指导你管理警报事件的取证步骤。 例如：

- 分析最近的设备活动（数据挖掘报表）。 

- 分析其他同时发生的事件（事件时间线）。 

- 分析全面的事件流量（PCAP 文件）。

## <a name="pcap-files"></a>PCAP 文件

在有些情况下，可以从警报消息访问 PCAP 文件。 如果需要详细了解关联的网络流量，这可能很有用。

若要下载 PCAP 文件，请选择 :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="“下载”图标":::（位于 “警报详细信息”对话框的右上角）。

## <a name="recommendations-for-investigating-an-event"></a>有关调查事件的建议 

警报的“建议”区域会显示有助于你更好地了解事件的信息。 在管理警报事件或对设备或网络采取操作之前，请先查看这些信息。

## <a name="see-also"></a>另请参阅

[加速警报工作流](how-to-accelerate-alert-incident-response.md)

[管理警报事件](how-to-manage-the-alert-event.md)
