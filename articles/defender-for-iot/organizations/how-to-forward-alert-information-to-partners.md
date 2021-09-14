---
title: 转发警报信息
description: 你可以通过使用转发规则将警报信息发送到合作伙伴系统。
ms.date: 08/29/2021
ms.topic: how-to
ms.openlocfilehash: 2136a58a383bb623edca69cb03c1c9c5530a107f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432340"
---
# <a name="forward-alert-information"></a>转发警报信息

可以将警报信息发送给与 Azure Defender for IoT 集成的合作伙伴、syslog 服务器、电子邮件地址等。 使用转发规则可以快速将警报信息传递给安全利益干系人。

定义触发转发规则所依据的条件。 使用转发规则条件可帮助确定和管理从传感器发送到外部系统的信息量。

系统会提供 Syslog 和其他默认转发操作。 与合作伙伴供应商（如 Microsoft Azure Sentinel、ServiceNow 或 Splunk）集成时，可能会出现更多转发操作。

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="警报信息。":::

Defender for IoT 管理员有权使用转发规则。

## <a name="about-forwarded-alert-information"></a>关于转发警报信息

警报提供了有关大量安全和操作事件的信息。 例如：

- 警报日期和时间

- 检测到事件的引擎

- 警报标题和描述性消息

- 警报严重性

- 源名称和目标名称及 IP 地址

- 检测到恶意流量

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="检测到地址扫描":::

创建转发规则时，会将相关信息发送到合作伙伴系统。

## <a name="about-forwarding-rules-and-certificates"></a>关于转发规则和证书

某些转发规则允许在传感器或本地管理控制台与集成供应商的服务器之间进行加密和证书验证。

在这些情况下，传感器或本地管理控制台是会话的客户端和发起端。  证书通常是从服务器接收的，或使用非对称加密，在此加密模式下，将提供特定的证书来设置集成。

Defender for IoT 系统已设置为验证证书或忽略证书验证。  有关启用和禁用验证的信息，请参阅[关于证书验证](how-to-deploy-certificates.md#about-certificate-validation)。

如果启用了验证但无法验证证书，则 Defender for IoT 和服务器之间的通信将暂停。  传感器将显示一条指示验证失败的错误消息。  如果禁用了验证且证书无效，则仍会进行通信。

以下转发规则允许加密和证书验证：
- Syslog CEF
- Azure Sentinel
- QRadar

## <a name="create-forwarding-rules"></a>创建转发规则

在传感器上新建转发规则：

1. 登录传感器。

1. 先在侧边菜单上选择“**转发**”。

1. 选择“创建转发规则”。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule-screen.png" alt-text="创建转发规则图标。":::

1. 输入转发规则的名称。

1. 选择严重性级别。

   这是在严重性级别方面要转发的最小事件。 例如，如果选择“**次要**”、“次要警报”，则将转发此严重级别以上的任何警报。 严重性级别已预先定义。

1. 选择要应用的任何协议。

   如果检测到的流量在特定协议上运行，则仅触发转发规则。 从下拉列表中选择所需的协议，或选择全部协议。

1. 选择应当应用此规则的引擎。

   选择所需的引擎，或选择全部引擎。 将发送所选引擎发出的警报。 

1. 选择要应用的操作，并填写所选操作所需的任何参数。

   转发规则操作指示传感器将警报信息转发给合作伙伴供应商或服务器。 可以为每个转发规则创建多个操作。

1. 根据需要添加其他操作。

1. 选择“提交”。

### <a name="email-address-action"></a>电子邮件地址操作

发送包含警报信息的邮件。 你可以为每个规则输入一个电子邮件地址。

为转发规则定义电子邮件：

1. 输入单个电子邮件地址。 如果需要添加多个电子邮件，你将需要为每个电子邮件地址创建另一个操作。

1. 请在 SIEM 上输入警报检测的时间戳的时区。

1. 选择“提交”。

### <a name="syslog-server-actions"></a>Syslog 服务器操作

支持以下格式：

- 短信

- CEF 消息

- LEEF 消息

- 对象消息

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="转发规则操作。":::

输入以下参数：

- Syslog 主机名称和端口。

- 协议 TCP 和 UDP。

- SIEM 上警报检测的时间戳的时区。

- TLS 加密证书文件和 CEF 服务器的密钥文件（可选）。

:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="为转发规则配置加密。":::

| Syslog 文本消息输出字段 | 说明 |
|--|--|
| 日期和时间 | Syslog 服务器计算机接收信息的日期和时间。 |
| 优先级 | 用户警报 |
| 主机名 | 传感器 IP 地址 |
| 协议 | TCP 或 UDP |
| Message | 传感器：传感器名称。<br /> 警报：警报标题。<br /> 类型：警报类型。 可能是“**违反协议**”、“**政策冲突**”、“**恶意软件**”、“**异常**”或“**正常工作**”。<br /> 严重性：警报严重性。 可能是“**警告**”、“**次要**”、“**主要**”或“**严重**”。<br /> 源：验证源设备名称。<br /> 源 IP：源设备 IP 地址。<br /> 目标：目标设备名称。<br /> 目标 IP：目标设备的 IP 地址。<br /> 消息：警报消息。<br /> 警报组：与警报关联的警报组。 |

| Syslog 对象输出 | 说明 |
|--|--|
| 日期和时间 | Syslog 服务器计算机接收信息的日期和时间。 |  
| 优先级 | 用户警报 |
| 主机名 | 传感器 IP |
| Message | 传感器名称：设备名称。 <br /> 警报时间：检测到警报的时间：可能与 syslog 服务器计算机的时间不同，具体取决于转发规则的时区配置。 <br /> 警报标题：警报的标题。 <br /> 警报消息：警报发出的消息。 <br /> 警报严重性：警报的严重性：**警告**、**次要**、**主要** 或 **严重**。 <br /> 警报类型：**违反协议**、**政策冲突**、**恶意软件**、**异常** 或 **正常工作**。 <br /> 协议：警报的协议。  <br /> **Source_MAC**：源设备的 IP 地址、名称、供应商或操作系统。 <br /> Destination_MAC：目标设备的 IP 地址、名称、供应商或操作系统。 如果缺少数据，则该值将为 **N/A**。 <br /> alert_group：与警报关联的警报组。 |

| Syslog CEF 输出格式 | 说明 |
|--|--|
| 日期和时间 | Syslog 服务器计算机接收信息的日期和时间。 |
| 优先级 | 用户警报 |
| 主机名 | 传感器 IP 地址 |
| Message | CEF：0 <br />适用于 IoT 的 Azure Defender <br />传感器名称：传感器设备名称。 <br />传感器版本 <br />警报标题：警报的标题。 <br />msg：警报消息。 <br />协议：警报的协议。 <br />严重性： **警告**、**次要**、**主要** 或 **严重**。 <br />类型： **违反协议**、**政策冲突**、**恶意软件**、**异常** 或 **正常工作**。 <br /> 开始时间：检测到警报的时间。 <br />可能不同于 syslog 服务器计算机的时间，具体取决于转发规则的时区配置。 <br />src_ip：源设备的 IP 地址。  <br />dst_ip：目标设备的 IP 地址。<br />cat：与警报关联的警报组。  |

| Syslog LEEF 输出格式 | 说明 |
|--|--|
| 日期和时间 | Syslog 服务器计算机接收信息的日期和时间。 |  
| 优先级 | 用户警报 |
| 主机名 | 传感器 IP |
| Message | 传感器名称： Azure Defender for IoT 设备的名称。 <br />LEEF：1.0 <br />适用于 IoT 的 Azure Defender <br />传感器  <br />传感器版本 <br />Azure Defender for IoT 警报 <br />标题：警报标题。 <br />msg：警报消息。 <br />协议：警报的协议。<br />严重性： **警告**、**次要**、**主要** 或 **严重**。 <br />类型：警报的类型：**违反协议**、**政策冲突**、**恶意软件**、**异常** 或 **正常工作**。 <br />开始时间：警报开始时间它可能不同于 syslog 服务器计算机的时间。 （具体取决于时区配置。） <br />src_ip：源设备的 IP 地址。<br />dst_ip：目标设备的 IP 地址。 <br />cat：与警报关联的警报组。 |

输入信息后，选择“**提交**”。

### <a name="webhook-server-action"></a>Webhook 服务器操作

将警报信息发送到 Webhook 服务器。 使用 Webhook 服务器可以设置与 Defender for IoT 的集成，此类集成可订阅警报事件。 触发警报事件时，管理控制台会将 HTTP POST 有效负载发送到 Webhook 的已配置 URL。 Webhook 可用于更新外部 SIEM 系统、SOAR 系统、事件管理系统等。

若要定义 Webhook 操作，请执行以下操作：

1. 选择 Webhook 操作。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/webhook.png" alt-text="定义 Webhook 转发规则。":::

1. 在“URL”字段中输入服务器地址。

1. 在“键”和“值”字段中，使用键和值定义来自定义 HTTP 标头。 键只能包含字母、数字、短划线以及下划线。 值只能包含一个前导空格和/或一个尾随空格。

1. 选择“保存”。

### <a name="webhook-extended"></a>Webhook 扩展

可以使用 Webhook 扩展将附加的数据发送到终结点。 扩展功能包括 Webhook 警报中的所有信息，并将以下信息添加到报告：

- sensorID
- sensorName
- zoneID
- zoneName
- siteID
- siteName
- sourceDeviceAddress
- destinationDeviceAddress
- remediationSteps
- handled
- additionalInformation

若要定义 webhook 扩展操作，请执行以下操作：

1. 在管理控制台中，从左侧窗格中选择“转发”。

1. 通过选择 :::image type="icon" source="media/how-to-forward-alert-information-to-partners/add-icon.png" border="false"::: 按钮添加转发规则。

1. 为转发警报添加有意义的名称。

1. 选择严重性级别。

1. 选择 **添加** 。

1. 在“选择类型”下拉列表窗口中，选择“Webhook 扩展”。

   :::image type="content" source="media/how-to-forward-alert-information-to-partners/webhook-extended.png" alt-text="从“选择类型”下拉选项菜单中选择“Webhook 扩展”选项。":::

1. 在“URL”字段中添加终结点数据 URL。

1. （可选）使用键和值定义自定义 HTTP 头。 通过选择 :::image type="icon" source="media/how-to-forward-alert-information-to-partners/add-header.png" border="false"::: 按钮添加额外的头。

1. 选择“保存”。

配置 Webhook 扩展转发规则后，可以从管理控制台上的“转发”屏幕测试警报。

若要测试 Webhook 扩展转发规则，请执行以下操作：

1. 在管理控制台中，从左侧窗格中选择“转发”。

1. 选择“运行”按钮以测试警报。

    :::image type="content" source="media/how-to-forward-alert-information-to-partners/run-button.png" alt-text="选择“运行”按钮以测试转发规则。":::

如果看到“成功”通知，则表示转发规则正常工作。

:::image type="content" source="media/how-to-forward-alert-information-to-partners/success.png" alt-text="如果显示成功通知，则表示警报工作正常。":::

### <a name="netwitness-action"></a>NetWitness 操作

将警报信息发送到 NetWitness 服务器。

定义 NetWitness 转发参数：

1. 输入 NetWitness **主机名** 和 **端口** 信息。

1. 请在 SIEM 上输入警报检测的时间戳的时区。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="将时区添加到转发规则。":::

1. 选择“提交”。

### <a name="integrated-vendor-actions"></a>集成供应商操作

你可能已将系统与安全、设备管理或其他行业供应商集成。 这些集成使你能够：

  - 发送警报信息。

  - 发送设备清单信息。

  - 与供应商端防火墙通信。

集成有助于桥接以前孤立的安全解决方案，增强设备的可见性，并加速系统范围内的响应，从而更快地降低风险。

使用 "操作" 部分输入与集成供应商进行通信所需的凭据和其他信息。

有关为集成设置转发规则的详细信息，请参阅相关的合作伙伴集成文章。

## <a name="test-forwarding-rules"></a>测试转发规则

测试转发规则中定义的传感器和伙伴服务器之间的连接：

1. 从“**转发规则**”对话框中选择规则。

1. 选择“**更多**”对话框。

1. 选择“**发送测试消息**”。

1. 请前往你的合作伙伴系统，验证是否已收到该传感器发送的信息。

## <a name="edit-and-delete-forwarding-rules"></a>编辑和删除转发规则 

编辑转发规则：

- 在“**转发规则**”屏幕上，在“**更多**”下拉菜单中选择“**编辑**”。 进行所需的更改，然后选择“**提交**”。

删除转发规则：

- 在“**转发规则**”屏幕上，在“**更多**”下拉菜单中选择“**删除**”。 在“**警告**”对话框中选择“**确定**”。

## <a name="forwarding-rules-and-alert-exclusion-rules"></a>转发规则和警报排除规则

管理员可能已经定义了警报排除规则。 这些规则通过指示传感器忽略基于各种参数的警报事件，帮助管理员更精细地控制警报触发。 这些参数可能包括设备地址、警报名称或特定传感器。

这意味着，根据管理员创建的排除规则，可能会忽略您定义的转发规则。 在本地管理控制台中定义了排除规则。

## <a name="see-also"></a>另请参阅

[加速警报工作流](how-to-accelerate-alert-incident-response.md)
