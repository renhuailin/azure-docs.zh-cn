---
title: 通过本地管理控制台管理传感器
description: 了解如何通过管理控制台管理传感器，包括更新传感器版本，将系统设置推送到传感器，管理证书，以及启用和禁用传感器上的引擎。
ms.date: 07/08/2021
ms.topic: how-to
ms.openlocfilehash: dbdee98fc1f7e6bc294bd54d72e64fa8f053eb8e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431181"
---
# <a name="manage-sensors-from-the-management-console"></a>通过管理控制台管理传感器

本文介绍如何通过管理控制台管理传感器，包括以下操作：

- 将系统设置推送到传感器

- 启用和禁用传感器上的引擎

- 更新传感器版本

## <a name="push-configurations"></a>推送配置

可以定义各种系统设置，并自动将其应用于已连接到管理控制台的传感器。 这样可以节省时间，并有助于确保简化每个企业传感器的设置。

可以在管理控制台中定义以下传感器系统设置：

- 邮件服务器

- SNMP MIB 监视

- Active Directory

- DNS 设置

- 子网

- 端口别名

若要应用系统设置，请执行以下操作：

1. 在控制台的左侧窗格中选择“系统设置”。

1. 在“配置传感器”窗格中选择一个选项。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="传感器的系统设置选项。":::

   以下示例说明如何为企业传感器定义邮件服务器参数。

1. 选择“邮件服务器”。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="在“系统设置”屏幕中选择你的邮件服务器。":::

1. 在左侧选择一个传感器。

1. 设置邮件服务器参数，然后选择“复制”。 传感器树中的每个项旁边都显示了一个复选框。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="确保已选中与你的传感器对应的复选框。":::

1. 在传感器树中，选择要将配置应用到的项。

1. 选择“保存”。

## <a name="update-sensor-versions"></a>更新传感器版本

可以在本地管理控制台中同时更新多个传感器。

### <a name="update-sequence"></a>更新序列

升级本地管理控制台和托管传感器时，请先更新管理控制台，然后再更新传感器。 如果不先更新本地管理控制台，则传感器更新过程将不会成功。

若要更新多个传感器，请执行以下操作：

1. 验证是否已将本地管理控制台更新到要更新传感器的版本。 有关本地管理控制台更新的详细信息，请参阅[更新软件版本](how-to-manage-the-on-premises-management-console.md#update-the-software-version)。

1. 转到 [Azure 门户](https://portal.azure.com/)。

1. 导航到 Azure Defender for IoT。

1. 转到“更新”页。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="“更新”仪表板视图的屏幕截图。":::

1. 在“传感器”部分选择“下载”，然后保存文件 。

1. 登录到管理控制台并选择“系统设置”。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="用于选择“系统设置”的“管理”菜单的屏幕截图。":::

1. 在“传感器引擎配置”部分选择要更新的传感器，然后选择“自动更新” 。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="显示学习模式和自动更新的两个传感器。":::

1. 选择“保存更改”。

1. 在管理控制台上选择“系统设置”。
1. 在“传感器版本更新”部分下，选择 :::image type="icon" source="../media/how-to-manage-sensors-from-the-on-premises-management-console/add-icon.png" border="false"::: 按钮。

    :::image type="content" source="../media/how-to-manage-sensors-from-the-on-premises-management-console/sendor-version-update-window.png" alt-text="在“传感器版本更新”窗口中，选择“+”图标以更新所有连接到管理控制台的传感器。":::

9. 此时会打开“上传文件”对话框。 上传从“更新”页下载的文件。

    :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="选择“浏览”按钮上传文件。":::

可以在“站点管理”窗口中监视每个传感器的更新状态。

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="观察更新进度。":::

### <a name="update-sensors-from-the-on-premises-management-console"></a>通过本地管理控制台更新传感器

可以从管理控制台查看传感器的更新状态。 如果更新失败，可以从本地管理控制台（版本 2.3.5 及以上）重新尝试更新。

**从本地管理控制台更新传感器：**

1. 登录到本地管理控制台，然后导航到“站点管理”页。

1. 在“更新进度”列下找到任何更新“失败”的传感器，然后选择下载按钮。 

    :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/download-update-button.png" alt-text="选择下载图标，尝试下载并安装传感器的更新。":::

可以在“站点管理”窗口中监视每个传感器的更新状态。

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="观察更新进度。":::

如果无法更新传感器，请与客户支持联系以获得帮助。

## <a name="update-threat-intelligence-packages"></a>更新威胁情报包 

威胁情报数据包随每个新的 Defender for IoT 版本提供，或者在版本发布间隔期按需提供。 该包包含特征码（包括恶意软件特征码）、CVE 和其他安全内容。 

可以从 Defender for IoT 门户的“更新”页手动上传此文件，并自动将其更新到传感器。 

**若要更新威胁情报数据，请执行以下操作：**

1. 转到 Defender for IoT 的“更新”页。 

1. 下载并保存文件。

1. 登录到管理控制台。 

1. 在边侧菜单中选择“系统设置”。 

1. 在“传感器引擎配置”部分，选择应接收更新的传感器。  

1. 在“选择威胁情报数据”部分，选择加号图标 ( **+** )。 

1. 上传从 Defender for IoT 的“更新”页下载的包。

## <a name="understand-sensor-disconnection-events"></a>了解传感器断开连接事件

如果传感器从其分配的本地管理控制台断开连接，“站点管理器”窗口将显示断开连接信息。 提供以下传感器断开连接信息：

- “本地管理控制台无法处理从传感器接收的数据。”

- “检测到时间偏差。 本地管理控制台已与传感器断开连接。”

- “传感器未与本地管理控制台通信。 请检查网络连接或验证证书。”

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="区域 1 视图的屏幕截图。":::

可以向第三方发送警报并提供有关已断开连接的传感器的信息。 有关详细信息，请参阅[转发传感器故障警报](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts)。

## <a name="enable-or-disable-sensors"></a>启用或禁用传感器

传感器受到五个 Defender for IoT 引擎的保护。 可以启用或禁用已连接的传感器的引擎。

| 引擎 | 说明 | 示例方案 |
|--|--|--|
| 协议冲突引擎 | 当数据包结构或字段值不符合协议规范时，将发生协议冲突。 | “非法 MODBUS 操作(函数代码零)”警报。 此警报指示主要设备向辅助设备发送了函数代码为 0 的请求。 根据协议规范，不允许执行此操作，辅助设备可能不会正确处理输入。 |
| 策略冲突引擎 | 与已获知或已配置的策略中定义的基线行为背离时，会发生策略冲突。 | “未授权的 HTTP 用户代理”警报。 此警报指示在设备上使用了未经策略获知或批准的应用程序作为 HTTP 客户端。 它可能是该设备上的新 Web 浏览器或新应用程序。 |
| 恶意软件引擎 | 恶意软件引擎检测恶意网络活动。 | “可疑的恶意活动(Stuxnet)”警报。 此警报指示传感器发现了与 Stuxnet 恶意软件相关的可疑网络活动。Stuxnet 恶意软件是一种手段高超的持久性威胁，以工业控制和 SCADA 网络为目标。 |
| 异常引擎 | 恶意软件引擎检测到网络行为存在异常。 | “信道中存在定期性的行为。” 此组件检查网络连接，并发现数据传输的定期性或周期性行为，这种行为在工业网络中很常见。 |
| 操作引擎 | 此引擎检测操作事件或有故障的实体。 | `Device is Suspected to be Disconnected (Unresponsive)` 警报。 当设备有预定义的一段时间不响应任何请求时，将触发此警报。 此警报可能指示设备关闭、断开连接或出现故障。
|

**若要启用或禁用已连接的传感器的引擎，请执行以下操作：**

1. 在控制台的左侧窗格中选择“系统设置”。

1. 在“传感器引擎配置”部分，选择引擎对应的“启用”或“禁用”  。
         
1. 选择“保存更改”。

   如果某个企业传感器上已启用的引擎存在不匹配情况，将会出现红色感叹号。 该引擎可能已直接从传感器中禁用。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="已启用的引擎不匹配。"::: 

## <a name="define-sensor-backup-schedules"></a>定义传感器备份计划

可以在本地管理控制台中计划传感器备份并执行按需传感器备份。 这有助于防范硬盘故障和数据丢失。

- 要备份的内容：配置和数据。

- 不要备份的内容：PCAP 文件和日志。 可以手动备份和还原 PCAP 与日志。

默认情况下，会在每天凌晨 3:00 自动备份传感器。 使用传感器备份计划功能可以收集这些备份，并将其存储在本地管理控制台或外部备份服务器上。 将文件从传感器复制到本地管理控制台的过程是通过一个已加密通道进行的。

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="传感器备份屏幕的视图。":::

更改默认的传感器备份位置后，本地管理控制台将自动从传感器上的新位置或外部位置检索文件，前提是控制台有权访问该位置。 

如果传感器未注册到本地管理控制台，“传感器备份计划”对话框将指示未有任何传感器受管理。  

不管文件存储在哪个位置，还原过程都是相同的。

### <a name="backup-storage-for-sensors"></a>传感器的备份存储

可以使用本地管理控制台维护每个受管理传感器的最多九个备份，前提是备份的文件不超过分配的最大备份空间。 

可用空间是根据所用的管理控制台型号计算的： 

- 生产型号：默认存储为 40 GB；限制为 100 GB。 

- 中等型号：默认存储为 20 GB；限制为 50 GB。 

- 便携型号：默认存储为 10 GB；限制为 25 GB。 

- 精简型号：默认存储为 2 GB；限制为 4 GB。 

- 加固型号：默认存储为 10 GB；限制为 25 GB。 

默认分配已显示在“传感器备份计划”对话框中。 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="“编辑邮件服务器配置”屏幕。":::

备份到外部服务器时没有存储限制。 但是，必须在“传感器备份计划” > “自定义路径”字段中定义分配上限 。 支持以下数字和字符：`/, a-z, A-Z, 0-9, and _`。 

下面是有关超过分配存储限制的信息：

- 如果超过分配的存储空间，则不会备份传感器。 

- 如果你要备份多个传感器，则管理控制台将尝试检索受管理传感器的传感器文件。  

- 如果从一个传感器检索的信息量超过限制，则管理控制台将尝试从下一个传感器检索备份信息。 

超过定义的保留备份数时，将删除最旧的备份文件，以容纳新的备份文件。

将使用以下格式自动为传感器备份文件命名：`<sensor name>-backup-version-<version>-<date>.tar`。 例如：`Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`。 

**若要备份传感器，请执行以下操作：**

1. 在“系统设置”窗口中选择“计划传感器备份” 。 本地管理控制台管理的传感器将显示在“传感器备份计划”对话框中。  

1. 启用“收集备份”切换开关。  

1. 选择日历间隔、日期和时区。 时间格式基于 24 小时制。 例如，输入 6:00 PM 会显示为 18:00。 

1. 在“备份存储分配”字段中，输入要为备份分配的存储。 如果超过最大空间，你将收到通知。

1. 在“保留最后一个”字段中，指定你要为每个传感器保留的备份数。 超过此限制时，将删除最旧的备份。  

1. 选择备份位置：  

   - 若要备份到本地管理控制台，请禁用“自定义路径”切换开关。 默认位置为 `/var/cyberx/sensor-backups`。  

   - 若要备份到外部服务器，请启用“自定义路径”切换开关并输入位置。 支持以下数字和字符：`/, a-z, A-Z, 0-9, and, _`。 

1. 选择“保存”。 

**若要立即备份，请执行以下操作：**

- 选择“立即备份”。 本地管理控制台将创建和收集传感器备份文件。 

### <a name="receiving-backup-notifications-for-sensors"></a>接收传感器的备份通知 

“传感器备份计划”对话框和备份日志会自动列出有关备份成功和失败的信息。  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="查看传感器、其所在位置以及所有相关信息。":::

失败的可能原因包括：    

- 找不到备份文件。 

- 已找到文件，但无法检索该文件。  

- 出现网络连接故障。 

- 未为本地管理控制台分配足够的空间，因此它无法完成备份。  

失败时你可以发送电子邮件通知、syslog 更新和系统通知。 为此，请在“系统通知”中创建转发规则。 

### <a name="restoring-sensors"></a>还原传感器 

可以从本地管理控制台和使用 CLI 还原备份。  

若要从控制台还原，请执行以下操作：

- 在“传感器系统设置”窗口中选择“还原映像” 。

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="还原映像的备份。":::

  然后，控制台将显示还原失败。  

若要使用 CLI 还原，请执行以下操作： 

- 登录到管理帐户并输入 `$ sudo cyberx-xsense-system-restore`。 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>将传感器备份保存到外部 SMB 服务器

**若要设置 SMB 服务器以便将传感器备份保存到外部驱动器，请执行以下操作：**

1. 在外部 SMB 服务器中创建一个共享文件夹。 

1. 获取访问 SMB 服务器所需的文件夹路径、用户名和密码。 

1. 在 Defender for IoT 中，为备份创建目录： 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

1. 编辑 fstab：  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

1. 编辑或创建要共享的凭据。 这是 SMB 服务器的凭据： 

   `sudo nano /etc/samba/user` 

1. 添加：  

   `username=<user name>` 

   `password=<password>` 

1. 装载目录： 

   `sudo mount -a` 

1. 在 Defender for IoT 传感器上的共享文件夹中配置一个备份目录：  

   `sudo nano /var/cyberx/properties/backup.properties` 

1. 将 `Backup.shared_location` 设置为 `<backup_folder_name_on_cyberx_server>`。

## <a name="see-also"></a>另请参阅

[管理单个传感器](how-to-manage-individual-sensors.md)
