---
title: 管理专用协议 (Horizon)
description: Defender for IoT Horizon 提供一个开放开发环境 (ODE)，该环境用于保护运行专有协议的 IoT 和 ICS 设备。
ms.date: 12/12/2020
ms.topic: reference
ms.openlocfilehash: cf38c79d3703fd1beb3a402587b7d388e89ce14e
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114673828"
---
# <a name="defender-for-iot-horizon"></a>Defender for IoT Horizon

Defender for IoT Horizon 包含一个开放开发环境 (ODE)，该环境用于保护运行专有协议的 IoT 和 ICS 设备。

Horizon 提供：

  - 对通用协议、专有协议、自定义协议或者偏离任何标准的协议的无限制完全支持。 
  - 全新的灵活度和范围用于 DPI 开发。
  - 一个可呈指数级提高 OT 可见性和控制度的工具，而无需升级到新版本。
  - 实现专有开发的安全性，不会泄漏敏感信息。

使用 Horizon SDK 可以设计剖析器插件以用于解码网络流量，这样，流量便可由自动化的 Defender for IoT 网络分析程序处理。

协议剖析器是作为外部插件开发的，与多种不同的 Defender for IoT 服务（例如，提供监视、警报和报告功能的服务）集成。

有关使用开放式开发环境 (ODE) SDK 和创建协议插件的详细信息，请联系 <ms-horizon-support@microsoft.com>。

开发插件后，可以使用 Horizon Web 控制台执行以下操作：

  - 上传插件
  - 启用和禁用插件  
  - 监视和调试插件以评估性能
  - 基于专有协议创建自定义警报。 在控制台中显示警报，并将其转发到合作伙伴供应商。 

    :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-plugin.png" alt-text="通过 Horizon 插件上传。"::: 

此功能适用于管理员、Cyberx 或支持用户。

若要登录到 Horizon 控制台，请执行以下操作：

1. 通过 CLI 登录到传感器。
2. 在文件 `/var/cyberx/properties/horizon.properties` 中，将 `ui.enabled` 属性更改为 `true` (`horizon.properties:ui.enabled=true`)
3. 登录到传感器控制台。 
4. 从主菜单中选择“Horizon”。 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-from-the-menu.png" alt-text="从主菜单中选择“Horizon”。":::  

Horizon 控制台将显示 Defender for IoT 提供的基础结构插件，以及你创建并上传的任何其他插件。 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/infrastructure.png" alt-text="Horizon 基础结构的屏幕截图。":::

## <a name="upload-plugins"></a>上传插件

创建并测试专有剖析器插件后，可以从 Horizon 控制台将其上传并对其进行监视。

若要上传，请执行以下操作：

1. 在控制台中选择“上传”。

   :::image type="content" source="media/how-to-manage-proprietary-protocols/upload-a-plugin.png" alt-text="选择插件对应的“上传”。":::

2. 拖动或浏览到你的插件。 如果上传失败，将显示错误消息。

有关使用开放式开发环境 (ODE) SDK 和创建协议插件的详细信息，请联系 <ms-horizon-support@microsoft.com>。

## <a name="enable-and-disable-plugins"></a>启用和禁用插件

使用切换按钮来启用和禁用插件。 禁用后，将不再监视流量。

无法禁用基础结构插件。

## <a name="monitor-plugin-performance"></a>监视插件性能 

Horizon 控制台“概述”窗口提供有关已上传的插件的基本信息，并可用于禁用和启用插件。

:::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-overview.png" alt-text="Horizon 概述页的屏幕截图。"::: 

| 应用程序 | 上传的插件的名称。 |
|--|--|
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/toggle-icon.png" border="false"::: | 启用或禁用插件。 禁用插件后，传感器不会处理该插件中定义的协议流量。 |
| 时间 | 上次分析数据的时间。 每五秒更新一次。 |
| PPS | 每秒的数据包数。 |
| 带宽 | 在过去五秒内检测到的平均带宽。 |
| 不当格式 | 在协议通过了验证后使用了格式不当的验证。 如果未能基于协议处理数据包，将返回失败响应。<br/> <br />此列指示过去五秒内发生的不当格式错误数。 |
| 警告 | 数据包与结构和规范匹配，但基于插件警告配置检测到了意外的行为。 |
| 错误 | 通不过基本协议验证（用于验证数据包是否与协议定义匹配）的数据包数。  此处显示的数字指示在过去五秒内检测到的错误数。 |
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/monitor-icon.png" border="false"::: | 请查看有关针对插件检测到的格式不当错误和警告的详细信息。 |

### <a name="plugin-performance-details"></a>插件性能详细信息

可以通过分析针对插件检测到的格式不当错误和警告数，来实时监视插件性能。 可通过一个选项冻结屏幕，并导出信息供进一步调查

:::image type="content" source="media/how-to-manage-proprietary-protocols/snmp-monitor.png" alt-text="SNMP 监视器概述的屏幕截图。":::

### <a name="horizon-logs"></a>Horizon 日志

可以在剖析详细信息、剖析日志和导出日志中导出 Horizon 剖析信息。

:::image type="content" source="media/how-to-manage-proprietary-protocols/export-logs-details.png" alt-text="导出日志的剖析详细信息。":::

## <a name="trigger-horizon-alerts"></a>触发 Horizon 警报 

通过基于 Horizon 框架流量剖析器针对任何协议触发自定义警报，来增强企业中的警报管理。 

这些警报可用于传达信息：  

  - 关于基于专有 Horizon 插件中的协议和基础协议的流量检测。

  - 关于所有协议层中协议字段的组合。 例如，在运行 MODBUS 的环境中，你可能希望在传感器检测到对特定 IP 地址和以太网目标上的内存寄存器发出 write 命令时生成警报，或者在对特定 IP 地址执行任何访问时生成警报。

满足 Horizon 警报条件和规则条件时，将触发警报。

  :::image type="content" source="media/how-to-manage-proprietary-protocols/custom-alert-rules.png" alt-text="Horizon 的示例自定义规则。":::

此外，使用 Horizon 自定义警报可以编写自己的警报标题和消息。 还可以将已解析的协议字段和值嵌入到警报消息文本中。

使用基于条件的自定义警报触发和消息传递可帮助精准查出特定的网络活动，并有效地将最新情况告知安全、IT 和运营团队。

### <a name="working-with-horizon-alerts"></a>处理 Horizon 警报

Horizon 自定义警报规则生成的警报将显示在传感器和管理控制台的“警报”窗口中，如果使用了“转发规则”，则这些警报还会显示在集成的合作伙伴系统中。 

可以确认或屏蔽 Horizon 生成的警报。 “了解”选项不适用于自定义警报，因为无法在策略基线中了解警报事件。

使用“转发规则”时，警报信息将转发到合作伙伴供应商。

Horizon 自定义警报的严重性为“严重”。

Horizon 自定义警报在“管理此事件”部分下包含静态文本，指示该警报是组织的安全团队生成的。

### <a name="required-permissions"></a>所需的权限

定义为 Defender for IoT 用户的用户有权创建 Horizon 自定义警报规则。

### <a name="about-creating-rule-conditions"></a>关于创建规则条件

规则条件描述要检测到哪种网络流量才会触发警报。 规则条件可由一个或多个字段、运算符和值集构成。 使用 AND 创建条件集。

满足规则条件或条件集时，将发送警报。 如果条件逻辑无效，你将收到通知。

  :::image type="content" source="media/how-to-manage-proprietary-protocols/and-condition.png" alt-text="在自定义规则中使用 AND 条件。":::

还可以为一个协议创建多个规则。 这意味着，在满足规则条件时，将针对创建的每个规则触发警报。

### <a name="about-titles-and-messages"></a>关于标题和消息

警报消息可以包含输入的字母数字字符，以及检测到的流量变量。 例如，可在警报消息中包含检测到的源和目标地址。 支持各种语言。

### <a name="about-alert-recommendations"></a>关于警报建议 

Horizon 自定义警报在“管理此事件”部分下包含静态文本，指示该警报是组织的安全团队生成的。 还可以使用警报注释来改善阅读警报的个人与团队之间的沟通。 

## <a name="create-horizon-alert-rules"></a>创建 Horizon 警报规则

本文将介绍如何创建警报规则。

若要创建 Horizon 自定义警报，请执行以下操作：

1. 在 Horizon 控制台的“插件”菜单中右键单击某个插件。

    :::image type="content" source="media/how-to-manage-proprietary-protocols/plugins-menu.png" alt-text="在菜单中右键单击某个插件。":::

2. 选择“Horizon 自定义警报”。 此时会打开所选插件的“规则”窗口。

    :::image type="content" source="media/how-to-manage-proprietary-protocols/sample-rule-window.png" alt-text="插件的示例规则窗口已打开。":::

3. 在“标题”字段中输入标题。

4. 在“消息”字段中输入警报消息。 使用大括号 `{}` 将检测到的字段参数包含在消息中。 输入左大括号时，会显示相关字段。

    :::image type="content" source="media/how-to-manage-proprietary-protocols/rule-window.png" alt-text="在规则窗口中使用 {} 以包含检测到的字段。":::

5. 定义警报条件。

   :::image type="content" source="media/how-to-manage-proprietary-protocols/define-conditions.png" alt-text="定义警报的条件。":::

6. 选择一个变量。 变量表示插件中配置的字段。

7. 选择一个运算符：

    - 等于
    
    - 不等于
    
    - 小于
    
    - 小于或等于
    
    - 大于
    
    - 大于或等于

8. 输入数字类型的值。 如果选择的变量是 MAC 地址或 IP 地址，则必须将值从点分十进制地址转换为十进制格式。 使用 IP 地址转换工具，例如 <https://www.ipaddressguide.com/ip>。

    :::image type="content" source="media/how-to-manage-proprietary-protocols/ip-address-value.png" alt-text="转换后的 IP 地址值。":::

9. 选择“AND”创建条件集。

10. 选择“保存”。 该规则随即会添加到“规则”部分。

### <a name="edit-and-delete-horizon-custom-alert-rules"></a>编辑和删除 Horizon 自定义警报规则

根据需要使用“编辑”和“删除”选项。 某些规则是嵌入的，无法编辑或删除。

### <a name="create-multiple-rules"></a>创建多个规则

如果创建了多个规则，只要任何规则条件或条件集有效，就会触发警报。

## <a name="see-also"></a>另请参阅

[查看警报中提供的信息](how-to-view-information-provided-in-alerts.md)
