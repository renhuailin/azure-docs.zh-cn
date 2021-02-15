---
title: 通过本地管理控制台管理传感器
description: 了解如何通过管理控制台管理传感器，包括更新传感器版本、将系统设置推送到传感器，以及启用和禁用传感器上的引擎。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 10051bfdf7a93a8d6c83d649a7b0ad008b5e2caf
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523951"
---
# <a name="manage-sensors-from-the-management-console"></a>通过管理控制台管理传感器

本文介绍如何通过管理控制台管理传感器，其中包括：

- 向传感器推送系统设置

- 启用和禁用传感器上的引擎

- 更新传感器版本

## <a name="push-configurations"></a>推送配置

你可以定义各种系统设置并将其自动应用于连接到管理控制台的传感器。 这可以节省时间，并有助于确保企业传感器的优化设置。

你可以从管理控制台定义以下传感器系统设置：

- 邮件服务器

- SNMP MIB 监视

- Active Directory

- DNS 设置

- 子网

- 端口别名

应用系统设置：

1. 在控制台的左窗格中，选择 " **系统设置**"。

2. 在 " **配置传感器** " 窗格上，选择其中一个选项。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="传感器的系统设置选项。":::

   以下示例介绍了如何为企业传感器定义邮件服务器参数。

3. 选择 " **邮件服务器**"。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="从 &quot;系统设置&quot; 屏幕中选择邮件服务器。":::

4. 选择左侧的传感器。

5. 设置邮件服务器参数，然后选择 " **复制**"。 传感器树中的每个项目旁边都有一个复选框。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="确保为传感器选中复选框。":::

6. 在传感器树中，选择要对其应用配置的项目。

7. 选择“保存” 。

## <a name="update-versions"></a>更新版本

可以同时从本地管理控制台更新多个传感器。

更新多个传感器：

1. 转到 [Azure 门户](https://portal.azure.com/)。

2. 请参阅 Azure Defender for IoT。

3. 请参阅 " **更新** " 页面。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="更新仪表板视图的屏幕截图。":::

4. 从 "**传感器**" 部分选择 "**下载**" 并保存文件。

5. 登录到管理控制台并选择 " **系统设置**"。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="用于选择系统设置的 &quot;管理&quot; 菜单的屏幕截图。":::

6. 在 " **传感器引擎配置** " 部分中标记要更新的传感器，然后选择 " **自动更新**"。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="显示学习模式和自动更新的两个传感器。":::

7. 选择“保存更改”。 

8. 在 " **传感器版本升级** " 窗格上，选择 :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: 。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="用于显示文件的传感器版本升级屏幕。":::

9. " **上载文件** " 对话框将打开。 上载从 " **更新** " 页下载的文件。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="选择 &quot;浏览&quot; 按钮上传文件。":::

10. 在更新过程中，每个传感器的更新状态将出现在 " **站点管理** " 窗口中。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="观察更新进度。":::

## <a name="update-threat-intelligence-packages"></a>更新威胁智能包 

每个新的 IoT for IoT 版本都提供了威胁情报包，或者在版本之间需要。 包包含签名 (包括恶意软件签名) 、标识符和其他安全内容。 

你可以从用于 IoT 门户的 **更新** 页面上手动上载此文件，并自动将其更新到传感器。 

更新威胁智能数据的步骤： 

1. 请参阅 "用于 IoT **更新** 的 Defender" 页。 

2. 下载并保存该文件。

3. 登录到管理控制台。 

4. 在侧边菜单中，选择 " **系统设置**"。 

5. 在 " **传感器引擎配置** " 部分中选择应接收更新的传感器。  

6. 在 " **选择威胁智能数据** " 部分中，选择加号 (**+**) 。 

7. 上载从 "用于 IoT **更新** 的 Defender" 页面下载的包。

## <a name="understand-sensor-disconnection-events"></a>了解传感器断开连接事件

如果传感器与分配的本地管理控制台断开连接，则 " **站点管理器** " 窗口将显示断开连接信息。 以下传感器断开连接信息可用：

- "本地管理控制台无法处理从传感器接收的数据。"

- 检测到时间偏差。 本地管理控制台已与传感器断开连接。 "

- "传感器不与本地管理控制台通信。 请检查网络连接或证书验证。

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="区域1视图的屏幕截图。":::

可以向第三方发送警报，其中包含有关已断开连接的传感器的信息。 有关详细信息，请参阅 [转发传感器故障警报](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts)。

## <a name="enable-or-disable-sensors"></a>启用或禁用传感器

传感器受 IoT 引擎的五个 Defender 保护。 可以启用或禁用连接传感器的引擎。

| 引擎 | 说明 | 示例方案 |
|--|--|--|
| 协议冲突引擎 | 当数据包结构或字段值不符合协议规范时，将发生协议冲突。 | "非法 MODBUS 操作 (函数代码零) " 警报。 此警报表明主要设备向辅助设备发送了具有函数代码0的请求。 根据协议规范，不允许这样做，辅助设备可能无法正确处理输入。 |
| 策略冲突引擎 | 发生策略冲突时，与已学习或已配置策略中定义的基线行为发生偏差。 | "未经授权的 HTTP 用户代理" 警报。 此警报表明，未学习或批准策略的应用程序将用作设备上的 HTTP 客户端。 这可能是该设备上的新 web 浏览器或应用程序。 |
| 恶意软件引擎 | 恶意软件引擎会检测恶意网络活动。 | "怀疑恶意活动 (Stuxnet) " 警报。 此警报表明传感器发现可疑的网络活动与 Stuxnet 恶意软件相关，这是一个针对工业控制和 SCADA 网络的高级持久威胁。 |
| 异常引擎 | 恶意软件引擎检测到网络行为中的异常。 | "通信通道中的定期行为"。 这是一个组件，它检查网络连接并查找数据传输的周期性或循环行为，这在工业网络中很常见。 |
| 操作引擎 | 此引擎检测操作事件或无法正常工作的实体。 | `Device is Suspected to be Disconnected (Unresponsive)` 发出. 当设备不响应预定义时间段内的任何请求时，将触发此警报。 这可能表示设备关闭、断开或故障。
|

启用或禁用连接传感器的引擎：

1. 在控制台的左窗格中，选择 " **系统设置**"。

2. 在 " **传感器引擎配置** " 部分，选择 " **启用** 或 **禁用** 引擎"。
         
3. 选择 " **保存更改**"。

   如果某个企业传感器上已启用的引擎不匹配，则会出现红色感叹号。 引擎可能已直接从传感器中禁用。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="已启用的引擎不匹配。"::: 

## <a name="define-sensor-backup-schedules"></a>定义传感器备份计划

你可以从本地管理控制台计划传感器备份并执行按需传感器备份。 这有助于防止硬盘故障和数据丢失。

- 备份的内容：配置和数据。

- 不备份的内容： PCAP 文件和日志。 可以手动备份和还原 PCAPs 和日志。

默认情况下，传感器会在每天凌晨3:00 自动备份。 传感器的备份计划功能使你可以收集这些备份，并将它们存储在本地管理控制台或外部备份服务器上。 将文件从传感器复制到本地管理控制台时，会通过加密通道进行。

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="传感器备份屏幕的视图。":::

更改默认的传感器备份位置时，本地管理控制台会自动从传感器或外部位置上的新位置检索文件，前提是该控制台有权访问该位置。 

当传感器未注册到本地管理控制台时，" **传感器备份计划** " 对话框将指示不会管理任何传感器。  

无论文件的存储位置如何，还原过程都是相同的。

### <a name="backup-storage-for-sensors"></a>传感器的备份存储

您可以使用本地管理控制台为每个托管传感器维持最多九个备份，前提是备份文件不会超过分配的最大备份空间。 

可用空间根据所使用的管理控制台模型进行计算： 

- **生产模型**：默认存储为 40 GB;限制为 100 GB。 

- **中型模型**：默认存储为 20 GB;限制为 50 GB。 

- **便携式计算机型号**：默认存储为 10 GB;限制为 25 GB。 

- **精简模型**：默认存储为 2 GB;限制为 4 GB。 

- **耐用模型**：默认存储为 10 GB;限制为 25 GB。 

默认分配将显示在 " **传感器备份计划** " 对话框中。 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="编辑邮件服务器配置屏幕。":::

备份到外部服务器时没有存储限制。 但是，必须在 "**传感器备份计划**"  >  **自定义路径** 字段中定义上限分配限制。 支持以下数字和字符： `/, a-z, A-Z, 0-9, and _` 。 

下面是有关超出分配存储限制的信息：

- 如果超过分配的存储空间，则不会备份该传感器。 

- 如果要备份多个传感器，则管理控制台将尝试检索托管传感器的传感器文件。  

- 如果从一个传感器检索超过此限制，则管理控制台将尝试从下一个传感器检索备份信息。 

如果超过了所定义的备份保留数量，则会删除最早的备份文件以容纳新的备份文件。

传感器备份文件按以下格式自动命名： `<sensor name>-backup-version-<version>-<date>.tar` 。 例如：`Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`。 

备份传感器：

1. 从 "**系统设置**" 窗口中选择 "**计划传感器备份**"。 本地管理控制台管理的传感器出现在 " **传感器备份计划** " 对话框中。  

2. 启用 " **收集备份** " 切换。  

3. 选择日历间隔、日期和时区。 时间格式基于24小时制。 例如，将 6:00 PM 输入为 **18:00**。 

4. 在 " **备份存储分配** " 字段中，输入要为备份分配的存储。 如果超过了最大空间，系统会通知你。

5. 在 " **保留最后一个** " 字段中，指示要保留的每个传感器的备份数。 超过此限制时，将删除最早的备份。  

6. 选择备份位置：  

   - 若要备份到本地管理控制台，请禁用 " **自定义路径** " 切换。 默认位置为 `/var/cyberx/sensor-backups`。  

   - 若要备份到外部服务器，请启用 " **自定义路径** " 切换，然后输入位置。 支持以下数字和字符： `/, a-z, A-Z, 0-9, and, _` 。 

7. 选择“保存” 。 

立即备份： 

- 选择 " **立即备份**"。 本地管理控制台创建和收集传感器备份文件。 

### <a name="receiving-backup-notifications-for-sensors"></a>接收传感器的备份通知 

" **传感器备份计划** " 对话框和备份日志会自动列出有关备份成功和失败的信息。  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="查看传感器及其所在位置以及所有相关信息。":::

出现失败的原因可能是：    

- 找不到备份文件。 

- 找到了文件，但无法检索。  

- 出现网络连接故障。 

- 没有足够的空间分配给本地管理控制台来完成备份。  

你可以在发生故障时发送电子邮件通知、syslog 更新和系统通知。 为此，请在 " **系统通知**" 中创建转发规则。 

### <a name="restoring-sensors"></a>还原传感器 

你可以从本地管理控制台和使用 CLI 还原备份。  

从控制台还原： 

- 从 "**传感器系统** 设置" 窗口中选择 "**还原映像**"。

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="还原映像的备份。":::

  然后，控制台将显示还原失败。  

使用 CLI 还原： 

- 登录到管理帐户，然后输入 `$ sudo cyberx-xsense-system-restore` 。 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>将传感器备份保存到外部 SMB 服务器

若要设置 SMB 服务器以便将传感器备份保存到外部驱动器，请执行以下操作： 

1. 在外部 SMB 服务器中创建共享文件夹。 

2. 获取访问 SMB 服务器所需的文件夹路径、用户名和密码。 

3. 在用于 IoT 的 Defender 中，为备份创建目录： 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. 编辑 fstab：  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. 编辑或创建要共享的凭据。 以下是 SMB 服务器的凭据： 

   `sudo nano /etc/samba/user` 

6. 添加：  

   `username=<user name>` 

   `password=<password>` 

7. 装载目录： 

   `sudo mount -a` 

8. 为 IoT 传感器的 Defender 上的共享文件夹配置备份目录：  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. 将 `Backup.shared_location` 设置为 `<backup_folder_name_on_cyberx_server>`。

## <a name="next-steps"></a>后续步骤

[管理单个传感器](how-to-manage-individual-sensors.md)
