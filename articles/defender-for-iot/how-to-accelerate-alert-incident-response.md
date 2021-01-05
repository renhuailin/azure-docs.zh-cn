---
title: 加速警报工作流
description: 改善警报和事件工作流。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 14d7a0de1cd29b8c07f90c759a4d423d7186fdb9
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838127"
---
# <a name="accelerate-alert-workflows"></a>加速警报工作流

本文介绍如何使用 Azure Defender IoT 中的警报注释、警报组和自定义警报规则加速警报工作流。  这些工具可帮助你：

- 分析和管理网络中检测到的大量警报事件。

- 确定并处理特定网络活动。

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>使用警报评论加速事件工作流

使用警报评论来改善调查警报事件时个人和团队之间的通信。

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="显示恶意活动的屏幕截图。":::

使用警报评论来改进：

- **工作流步骤**：提供警报缓解步骤。

- **工作流跟进**：通知已执行的步骤。

- **工作流指南**：提供有关事件的建议、见解或警告。

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="显示警报评论的屏幕截图。":::

每个警报中将显示可用选项的列表。 用户可以选择一条或多条消息。

添加警报注释：

1. 在侧边菜单中，选择 " **系统设置**"。

2. 在 " **系统设置** " 窗口中，选择 " **警报注释**"。

3. 在 " **添加注释** " 框中，输入注释文本。 使用最多50个字符。 不允许逗点。

4. 选择 **添加** 。

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>使用警报组加速事件工作流

警报组允许 SOC 团队在其 SIEM 解决方案中查看和筛选警报，然后基于企业安全策略和业务优先级管理这些警报。 例如，有关新检测的警报在发现组中进行组织。 此组包括用于处理新设备、新 Vlan、新用户帐户、新 MAC 地址等的警报。

为以下合作伙伴解决方案创建转发规则时，将应用警报组：

  - Syslog 服务器

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="创建转发规则的屏幕截图。":::

相关警报组显示在合作伙伴输出解决方案中。 

### <a name="requirements"></a>要求

警报组将显示在支持的合作伙伴解决方案中，前缀如下：

  - QRadar、ArcSight、Syslog CEF、Syslog LEEF 的 **猫**

  - Syslog 文本消息的 **警报组**

  - Syslog 对象的 **alert_group**

应在合作伙伴解决方案中配置这些字段，以显示警报组名称。 如果没有与警报组相关联的警报，则合作伙伴解决方案中的字段将显示 **NA**。

### <a name="default-alert-groups"></a>默认警报组

将自动定义以下警报组：
|  |  |  |
|--|--|--|
| 异常通信行为 | 自定义警报 | 远程访问 |
| 异常 HTTP 通信行为 | 发现 | 重新启动和停止命令 |
| 身份验证 | 固件更改 | 扫描 |
| 未经授权的通信行为 | 非法命令 | 传感器流量 |
| 带宽异常 | Internet 访问权限 | 可疑的恶意软件 |
| 缓冲区溢出 | 操作失败 | 可疑的恶意活动 |
| 命令失败 | 操作问题 |  |
| 配置更改 | 编程 |  |

警报组是预定义的。 有关与警报组关联的警报的详细信息以及创建自定义警报组的详细信息，请联系 [Microsoft 支持部门](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

## <a name="customize-alert-rules"></a>自定义警报规则

你可以基于单个传感器检测到的信息添加自定义警报规则。 例如，定义一个规则，该规则指示传感器根据协议) 中的源 IP、目标 IP 或命令 (触发警报。 当传感器检测到规则中定义的流量时，将生成警报或事件。

警报消息指示用户定义的规则触发了警报。

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="显示自定义警报的屏幕截图。":::

若要创建自定义警报规则：

1. 从传感器的侧菜单中选择 " **自定义警报** "。
1. 选择加号 (**+**) 以创建规则。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="显示用户定义规则的屏幕截图。":::

1. 定义规则名称。
1. 从 " **类别** " 窗格中选择类别或协议。
1. 定义特定的源和目标 IP 或 MAC 地址，或选择任何地址。
1. 添加条件。 条件列表及其属性对于每个类别都是唯一的。 可以为每个警报选择多个条件。
1. 指示规则是否触发 **警报** 或 **事件**。
1. 指定警报的严重性级别。
1. 指示警报是否包含 PCAP 文件。
1. 选择“保存”。

规则将添加到 " **自定义警报规则** " 列表中，您可以在其中查看基本规则参数、上次触发规则的时间，等等。 还可以从列表中启用和禁用规则。

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="用户添加的自定义规则的屏幕截图。":::

### <a name="see-also"></a>另请参阅

[查看警报中提供的信息](how-to-view-information-provided-in-alerts.md)

[管理警报事件](how-to-manage-the-alert-event.md)
