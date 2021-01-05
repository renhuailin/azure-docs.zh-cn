---
title: 排查传感器和本地管理控制台问题
description: 排查您的传感器和本地管理控制台问题，消除您可能遇到的任何问题。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: a57db4f88de4a3b32b4fb315fb331500f955d501
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838382"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>排查传感器和本地管理控制台问题

本文介绍了传感器和本地管理控制台的基本故障排除工具。 除了此处所述的项目，你还可以通过以下方式检查系统的运行状况：

**警报**：当监视流量的传感器接口已关闭时，会创建警报。 

**Snmp**：通过 snmp 监视传感器的运行状况。 Azure Defender for IoT 响应从授权监视服务器发送的 SNMP 查询。 

**系统通知**：当管理控制台控制传感器时，可以转发有关传感器备份失败和已断开连接传感器的警报。

## <a name="sensor-troubleshooting-tools"></a>传感器故障排除工具

### <a name="investigate-password-failure-at-initial-sign-in"></a>调查首次登录时的密码失败

首次登录到预配置的箭头传感器时，需要执行以下密码恢复：

1. 在 "用于 IoT 的 Defender" 登录屏幕上，选择 " **密码恢复** " 选项。 

   " **密码恢复** " 屏幕打开。 在此处，系统会提示你选择用户和订阅，并为你提供唯一标识符。

1. 在 "用于 IoT **网站和传感器** 的 Defender" 页上，选择 " **恢复我的密码** " 选项卡。

1. 输入在 **密码恢复** 屏幕上收到的唯一标识符，然后选择 " **恢复**"。 `password_recovery.zip`下载文件。

   > [!NOTE]
   > 请勿更改激活文件。 它是已签名的文件，如果你篡改此文件，则不会工作。

1. 在 **密码恢复** 屏幕上，上传 `password_recovery.zip` 文件，然后选择 " **下一步**"。

然后，你将接收到管理控制台的系统生成的密码。 

### <a name="investigate-a-lack-of-traffic"></a>调查缺乏流量

当传感器识别到某个配置的端口上没有流量时，控制台顶部会出现指示器。 此指标对所有用户可见。

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="未检测到流量的警报屏幕截图。":::
 
出现此消息时，您可以调查没有流量的位置。 请确保 span 电缆已连接，并且 span 体系结构中没有任何变化。  

有关支持和故障排除信息，请联系 [Microsoft 支持部门](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

### <a name="check-system-performance"></a>检查系统性能 

如果部署了新传感器，或者传感器工作缓慢或未显示任何警报，则可以检查系统性能。

若要检查系统性能：

1. 在仪表板中，确保 `PPS > 0` 。

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="示例仪表板的屏幕截图。"::: 

2. 从侧边菜单中，选择 " **设备**"。

3. 在 " **设备** " 窗口中，确保正在发现设备。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="确保已发现设备。":::

4. 从侧边菜单中，选择 " **数据挖掘**"。

5. 在 " **数据挖掘** " 窗口中，选择 " **全部** " 并生成一个报表。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="使用数据挖掘生成新报表。":::

6. 请确保报表包含数据。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="确保报表包含数据。":::

7. 从侧边菜单中，选择 " **趋势 & 统计**"。

8. 在 " **趋势 & 统计信息** " 窗口中，选择 " **添加小组件**"。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="通过选择来添加小组件。":::

9. 添加小组件，并确保其显示数据。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="确保小组件正在显示数据。":::

10. 从侧边菜单中，选择 " **警报**"。 此时将显示 " **警报** " 窗口。

11. 请确保已创建警报。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="确保已创建警报。":::


### <a name="investigate-a-lack-of-expected-alerts"></a>调查缺少预期的警报

如果 " **警报** " 窗口未显示预期的警报，请验证以下各项：

- 检查是否已在 " **警报** " 窗口中显示相同警报作为对其他安全实例的反应。 如果为 "是"，并且尚未处理此警报，则传感器控制台不会显示新的警报。

- 请确保未使用管理控制台中的 **警报排除** 规则排除此警报。 

### <a name="investigate-widgets-that-show-no-data"></a>调查不显示数据的小组件

当 **趋势 & 统计信息** "窗口中没有显示任何数据时，请执行以下操作：

- [检查系统性能](#check-system-performance)。

- 请确保已正确配置时间和区域设置，且未将其设置为将来的时间。 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>调查只显示广播设备的设备映射

当映射上显示的设备未彼此连接时，范围端口配置可能会出现问题。 也就是说，你可能只看到广播设备，无单播通信。

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="查看广播设备。":::

在这种情况下，需要验证是否可以仅查看广播流量。 然后，要求网络工程师修复 SPAN 端口配置，以便可以看到单播流量。

验证是否只看到广播流量：

- 在 " **数据挖掘** " 屏幕上，使用 " **全部** " 选项创建报表。 然后，查看是否只有广播和多播流量 (并且报表中不会) 出现单播通信。

或：

- 直接从交换机记录 PCAP，或使用 Wireshark 连接便携式计算机。

### <a name="connect-the-sensor-to-ntp"></a>将传感器连接到 NTP

你可以配置独立传感器和管理控制台，并将其与之相关的传感器连接到 NTP。

若要将独立传感器连接到 NTP：

- [请联系支持团队以获得帮助](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

将管理控制台控制的传感器连接到 NTP：

- 与 NTP 的连接是在管理控制台上配置的。 管理控制台控制自动获取 NTP 连接的所有传感器。

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>调查当地图上未显示设备，或者有多个与 internet 相关的警报

有时，ICS 设备配置有外部 IP 地址。 这些 ICS 设备不会显示在地图上。 Internet 云不是设备，而是出现在地图上。 这些设备的 IP 地址包含在云映像中。

出现同一问题的另一个迹象是出现了多个与 internet 相关的警报。

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="多个与 internet 相关的警报。":::

修复配置：

1. 右键单击设备映射上的云图标，然后选择 " **导出 IP 地址**"。 复制专用范围，并将其添加到子网列表。 有关详细信息，请参阅 [配置子网](how-to-control-what-traffic-is-monitored.md#configure-subnets)。

2. 为 internet 连接生成新的数据挖掘报表。

3. 在 "数据挖掘报表" 中，选择 :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: 进入管理员模式并删除 ICS 设备的 IP 地址。

### <a name="tweak-the-sensors-quality-of-service"></a>调整传感器的服务质量

若要保存网络资源，可以限制传感器用于日常过程的接口带宽。

若要限制接口带宽，请使用 `cyberx-xsense-limit-interface` 需要使用 sudo 权限运行的 CLI 工具。 此工具将获取以下参数：

  - `* -i`：接口 (示例： eth0) 。

  - `* -l`：限制 (示例： 30 kbit/1 mbit) 。 可以使用以下带宽单位： kbps、mbps、kbit、mbit 或 bps。

  - `* -c`：清除 (以清除接口带宽限制) 。

调整服务质量：

1. 以 IoT 用户身份登录到传感器 CLI，然后输入 `sudo cyberx-xsense-limit-interface-I eth0 -l value` 。

   例如： `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > 对于物理设备，请使用 em1 接口。

2. 若要清除接口限制，请输入 `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` 。

## <a name="on-premises-management-console-troubleshooting-tools"></a>本地管理控制台疑难解答工具

### <a name="investigate-a-lack-of-expected-alerts"></a>调查缺少预期的警报

如果 " **警报** " 窗口中未显示预期的警报，请验证以下各项：

- 检查是否已在 " **警报** " 窗口中显示相同警报作为对其他安全实例的反应。 如果是，并且尚未处理此警报，则不会显示新的警报。

- 验证是否未使用本地管理控制台中的 **警报排除** 规则排除此警报。  

### <a name="tweak-the-quality-of-service"></a>调整服务质量

若要保存网络资源，可以在设备与本地管理控制台之间的一个同步操作中限制发送到外部系统 (（如电子邮件或 SIEM) ）的警报数。

默认值为 50。 这意味着，在设备与本地管理控制台之间的一个通信会话中，外部系统将不会超过50个警报。 

若要限制警报数量，请使用 `notifications.max_number_to_report` 中提供的属性 `/var/cyberx/properties/management.properties` 。 更改此属性后，不需要重新启动。

调整服务质量：

1. 以 IoT 用户身份登录。 

2. 验证默认值：

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   将显示以下默认值：

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

3. 编辑默认设置：

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

4. 编辑以下行的设置：

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

5. 保存更改。 无需重启。

## <a name="export-information-for-troubleshooting"></a>导出信息以进行故障排除

除了用于监视和分析网络的工具外，还可以将信息发送给支持团队进行进一步调查。 当你导出日志时，传感器会自动生成一个一次性密码 (OTP) ，在单独的文本文件中对导出的日志唯一。 

导出日志：

1. 在左侧窗格中，选择 " **系统设置**"。

2. 选择“导出日志”。

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="将日志导出到系统支持。":::

3. 在 " **文件名" 框中** ，输入要用于日志导出的文件名。 默认值为当前日期。

4. 若要定义要导出的数据，请选择数据类别：  

    | 导出类别 | 说明 |
    |--|--|
    | **操作系统日志** | 选择此选项可获取有关操作系统状态的信息。 |
    | **安装/升级日志** | 选择此选项以调查安装和升级配置参数。 |
    | **系统稳定输出** | 选择此选项以检查系统性能。 |
    | **剖析日志** | 选择此选项以允许对协议剖析进行高级检查。 |
    | **操作系统内核转储** | 选择此选项可导出内核内存转储。 内核内存转储包含内核在此内核中出现问题时使用的所有内存。 转储文件的大小小于完整内存转储。 通常，转储文件的大小大约为系统物理内存的三分之一。 |
    | **转发日志** | 选择此选项以调查转发规则。 |
    | **SNMP 日志** | 选择此选项以接收 SNMP 运行状况检查信息。 |
    | **核心应用程序日志** | 选择此选项可导出有关核心应用程序配置和操作的数据。 |
    | **与 CM 日志通信** | 如果与管理控制台之间存在连续问题或连接中断，请选择此选项。 |
    | **Web 应用程序日志** | 选择此选项可获取有关从应用程序的 web 接口发送的所有请求的信息。 |
    | **系统备份** | 选择此选项可以导出所有系统数据的备份，以便调查系统的确切状态。 |
    | **剖析统计信息** | 选择此选项以允许对协议统计信息进行高级检查。 |
    | **数据库日志** | 选择此选项可以从系统数据库中导出日志。 调查系统日志有助于确定系统问题。 |
    | **配置** | 选择此选项可导出有关所有可配置参数的信息，以确保正确配置所有参数。 |

5. 若要选择所有选项，请选择 "**下一步" 选择****类别**。

6. 选择“导出日志”。

将导出的日志添加到已 **存档的日志** 列表。 将 OTP 从导出的日志中的单独消息和媒体发送到支持团队。 支持团队将只能使用用于对日志进行加密的唯一 OTP 来提取导出的日志。

存档日志列表最多可以包含五个项目。 如果列表中项目的数量超出该数字，则会删除最早的项目。

## <a name="see-also"></a>另请参阅

- [查看警报](how-to-view-alerts.md)

- [设置 SNMP MIB 监视](how-to-set-up-snmp-mib-monitoring.md)

- [了解传感器断开连接事件](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
