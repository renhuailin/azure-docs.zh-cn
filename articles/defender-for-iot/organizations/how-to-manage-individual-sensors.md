---
title: 管理单个传感器
description: 了解如何管理单个传感器，包括管理激活文件、执行备份和更新独立传感器。
ms.date: 08/25/2021
ms.topic: how-to
ms.openlocfilehash: fd708b2ab259b38ea6983c4d4a6dac319e416218
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424270"
---
# <a name="manage-individual-sensors"></a>管理单个传感器

本文介绍如何管理单个传感器。 任务包括管理激活文件、执行备份和更新独立传感器。

还可以在本地管理控制台中执行某些传感器管理任务，从中可以同时管理多个传感器。

你将使用 Azure 门户来加入和注册传感器。

## <a name="manage-sensor-activation-files"></a>管理传感器激活文件

你的传感器已通过 Azure 门户加入到 Azure Defender for IoT。 每个传感器是作为本地连接的传感器或连接到云的传感器加入的。

应将唯一的激活文件上传到部署的每个传感器。 有关何时以及如何使用新文件的详细信息，请参阅[上传新的激活文件](#upload-new-activation-files)。 如果无法上传文件，请参阅[排查激活文件上传问题](#troubleshoot-activation-file-upload)。

### <a name="about-activation-files-for-locally-connected-sensors"></a>关于本地连接的传感器的激活文件

本地连接的传感器与 Azure 订阅相关联。 本地连接的传感器的激活文件包含到期日期。 此日期前一个月，传感器控制台的顶部会出现一条警告消息。 只有在更新激活文件之后，该警告才会消失。

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="系统设置的屏幕截图。":::

即使激活文件已到期，你也仍可以继续使用 Defender for IoT 功能。 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>关于已连接到云的传感器的激活文件

已连接到云的传感器与 Defender for IoT Hub 相关联。 这些传感器不受激活文件时限的限制。 已连接到云的传感器的激活文件用于确保连接到 Defender for IoT Hub。

### <a name="upload-new-activation-files"></a>上传新的激活文件

在以下情况下，可能需要为加入的传感器上传新激活文件：

- 本地连接的传感器上的激活文件已到期。 

- 你想要在不同的传感器管理模式下工作。 

- 你想要将新的 Defender for IoT Hub 分配给已连接到云的传感器。

**若要添加新的激活文件，请执行以下操作：**

1. 转到“传感器管理”页。

2. 选择要为其上传新的激活文件的传感器。

3. 删除它。

4. 在新模式下或使用新的 Defender for IoT Hub 再次从“加入”页加入该传感器。

5. 在“下载激活文件”页中下载激活文件。

6. 保存文件。

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="从 Defender for IoT Hub 下载激活文件。":::

7. 登录到 Defender for IoT 传感器控制台。

8. 在传感器控制台中，选择“系统设置” > “重新激活” 。

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="“系统设置”屏幕中的“重新激活”选项。":::

9. 选择“上传”并选择已保存的文件。

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="上传已保存的文件。":::

10. 选择“激活”  。

### <a name="troubleshoot-activation-file-upload"></a>排查激活文件上传问题

如果激活文件无法上传，你将收到一条错误消息。 可能发生了以下事件：

- 对于本地连接的传感器：激活文件无效。 如果该文件无效，请转到 Defender for IoT 门户。 在“传感器管理”页上，选择其激活文件无效的传感器，并下载新的激活文件。

- 对于已连接到云的传感器：传感器无法连接到 Internet。 检查传感器的网络配置。 如果传感器需要通过 Web 代理建立连接才能访问 Internet，请验证是否已在“传感器网络配置”屏幕上正确配置了代理服务器。 验证防火墙和/或代理中是否允许 \*.azure-devices.net:443。 如果不支持通配符或者你想要获得更高的控制度，则应在防火墙和/或代理中打开特定 Defender for IoT Hub 的 FQDN。 有关详细信息，请参阅[参考 - IoT 中心终结点](../../iot-hub/iot-hub-devguide-endpoints.md)。  

- 对于已连接云的传感器：激活文件有效，但 Defender for IoT 拒绝了该文件。 如果无法解决此问题，可以从 Defender for IoT 门户的“站点和传感器”页下载另一个激活文件。 如果仍不起作用，请联系 Microsoft 支持部门。

## <a name="manage-certificates"></a>管理证书

安装传感器后，将生成一个本地自签名证书，该证书用于访问传感器 Web 应用程序。 首次登录到传感器时，系统会提示管理员用户提供 SSL/TLS 证书。 

传感器管理员可能需要更新初始登录后上传的证书。 例如，如果证书已过期，则可能会发生这种情况。

**若要更新证书，请执行以下操作：**

1. 选择“系统设置”。

1. 选择“SSL/TLS 证书”。

    :::image type="content" source="media/how-to-manage-individual-sensors/certificate-upload.png" alt-text="上传证书":::

1. 在“SSL/TLS 证书”对话框中，删除现有证书，然后添加新证书。

    - 添加证书名称。
    - 上传 CRT 文件和密钥文件。
    - 如有必要，请上传 PEM 文件。

如果上传失败，请与安全部门或 IT 管理员联系，或者查看[关于证书](how-to-deploy-certificates.md)中的信息。

如需更改证书验证设置，请按以下步骤操作：

1. 启用或禁用“启用证书验证”切换开关。 如果该选项已启用，但验证失败，则相关组件之间的通信将会停止，并在控制台中显示验证错误。 如果该选项已禁用，则不会执行证书验证。请参阅[关于证书验证](how-to-deploy-certificates.md#about-certificate-validation)了解详细信息。

1. 选择“保存”。

如需了解有关首次上传证书的详细信息，请参阅[首次登录和激活清单](how-to-activate-and-set-up-your-sensor.md#first-time-sign-in-and-activation-checklist)

## <a name="connect-a-sensor-to-the-management-console"></a>将传感器连接到管理控制台

本部分介绍如何确保在传感器与本地管理控制台之间建立连接。 如果你在与外界隔绝的网络中工作，并想要将设备和警报信息从传感器发送到管理控制台，则需要确保做到这一点。 建立这种连接还可让管理控制台将系统设置推送到传感器，并针对传感器执行其他管理任务。

若要建立连接，请执行以下操作：

1. 登录到本地管理控制台。

2. 选择“系统设置”。

3. 在“传感器设置 - 连接字符串”部分，复制自动生成的连接字符串。

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="在此屏幕中复制连接字符串。"::: 

4. 登录到传感器控制台。

5. 在左侧窗格中选择“系统设置”。

6. 选择“管理控制台连接”。

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="“管理控制台连接”对话框的屏幕截图。":::

7. 将连接字符串粘贴到“连接字符串”框中，然后选择“连接” 。

8. 在本地管理控制台的“站点管理”窗口中，将传感器分配到某个区域。

## <a name="change-the-name-of-a-sensor"></a>更改传感器的名称

可以更改传感器控制台的名称。 新名称将显示在控制台 Web 浏览器、各种控制台窗口和故障排除日志中。

更改传感器名称的过程根据传感器是本地连接的传感器还是已连接到云的传感器而异。 默认名称为 sensor。

### <a name="change-the-name-of-a-locally-connected-sensor"></a>更改本地连接的传感器的名称

若要更改名称：

1. 在控制台左侧窗格的底部，选择当前传感器标签。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="显示传感器标签的屏幕截图。":::

1. 在“编辑传感器名称”对话框中输入名称。

1. 选择“保存”。 将应用新名称。

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>更改已连接到云的传感器的名称

如果传感器注册为已连接到云的传感器，则传感器名称是按照注册期间分配的名称定义的。 该名称已包含在首次登录时上传的激活文件中。 若要更改传感器的名称，需要上传新的激活文件。

若要更改名称：

1. 在 Azure Defender for IoT 门户中，转到“站点和传感器”页。

1. 在“站点和传感器”页中删除该传感器。

1. 在“入门”页中选择“加入传感器”，使用新名称进行注册。

1. 下载新的激活文件。

1. 登录到 Defender for IoT 传感器控制台。

1. 在传感器控制台中，选择“系统设置”，然后选择“重新激活”。

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="上传激活文件以重新激活传感器。":::

1. 选择“上传”并选择已保存的文件。

1. 选择“激活”  。

## <a name="update-the-sensor-network-configuration"></a>更新传感器网络配置

传感器网络配置是在安装传感器期间定义的。 可以更改配置参数。 还可以设置代理配置。

如果创建了新的 IP 地址，可能需要重新登录。

若要更改配置，请执行以下操作：

1. 在边侧菜单中选择“系统设置”。

2. 在“系统设置”窗口中选择“网络” 。

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="配置网络设置。":::

3. 设置参数：

    | 参数 | 说明 |
    |--|--|
    | IP 地址 | 传感器 IP 地址 |
    | 子网掩码 | 掩码地址 |
    | 默认网关 | 默认网关地址 |
    | DNS | DNS 服务器地址 |
    | 主机名 | 传感器主机名 |
    | 代理 | 代理主机和端口名称 |

4. 选择“保存”。

## <a name="synchronize-time-zones-on-the-sensor"></a>同步传感器上的时区

可以配置传感器的时间和区域，使所有用户看到相同的时间和区域。

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="配置时间和区域。":::

| 参数 | 说明 |
|--|--|
| 时区 | 以下各项的时区定义：<br />- 警报<br />- 趋势和统计小组件<br />- 数据挖掘报告<br />   \- 风险评估报告<br />- 攻击途径 |
| 日期格式 | 选择以下格式选项之一：<br />- dd/MM/yyyy HH:mm:ss<br />- MM/dd/yyyy HH:mm:ss<br />- yyyy/MM/dd HH:mm:ss |
| 日期和时间 | 以你选择的格式显示当前日期和当地时间。<br />例如，如果你的实际位置为“美国纽约”，但时区设置为“欧洲柏林”，则会根据“柏林”当地时间显示时间。 |

若要配置传感器时间，请执行以下操作：

1. 在边侧菜单中选择“系统设置”。

2. 在“系统设置”窗口中选择“时间和区域” 。

3. 设置参数并选择“保存”。

## <a name="set-up-backup-and-restore-files"></a>设置备份和还原文件

将在每日凌晨 3:00 自动执行系统备份。 数据将保存到传感器中的不同磁盘上。 默认位置为 `/var/cyberx/backups`。

可以自动将此文件传输到内部网络。

> [!NOTE]
> - 只能在相同的版本之间执行备份和还原过程。
> - 在某些体系结构中已禁用备份。 可以在 `/var/cyberx/properties/backup.properties` 文件中将它启用。

通过本地管理控制台控制传感器时，可以使用传感器的备份计划来收集这些备份，并将其存储在管理控制台或外部备份服务器上。

要备份的内容：配置和数据。

不要备份的内容：PCAP 文件和日志。 可以手动备份和还原 PCAP 与日志。

将使用以下格式自动为传感器备份文件命名：`<sensor name>-backup-version-<version>-<date>.tar`。 示例为 `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`。

若要配置备份，请执行以下操作：

- 登录到管理帐户并输入 `$ sudo cyberx-xsense-system-backup`。

若要还原最新备份文件，请执行以下操作：

- 登录到管理帐户并输入 `$ sudo cyberx-xsense-system-restore`。

若要将备份保存到外部 SMB 服务器，请执行以下操作：

1. 在外部 SMB 服务器中创建一个共享文件夹。

    获取访问 SMB 服务器所需的文件夹路径、用户名和密码。

2. 在传感器中，为备份创建一个目录：

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. 编辑 `fstab`：

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. 为 SMB 服务器创建并编辑用于共享的凭据：

    `sudo nano /etc/samba/user` 

5. 添加：

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. 装载目录：

    `sudo mount -a`

7. 在 Defender for IoT 传感器上配置共享文件夹的备份目录：  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>还原传感器

可以从传感器控制台和使用 CLI 还原备份。

若要从控制台还原，请执行以下操作：

- 在传感器的“系统设置”窗口中选择“还原映像” 。

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="选择相应的按钮还原映像。":::

控制台将显示还原失败。

若要使用 CLI 还原，请执行以下操作：

- 登录到管理帐户并输入 `$ sudo cyberx-management-system-restore`。

## <a name="update-a-standalone-sensor-version"></a>更新独立传感器版本

以下过程说明如何使用传感器控制台更新独立传感器。 更新过程大约需要 30 分钟。

1. 转到 [Azure 门户](https://portal.azure.com/)。

2. 转到 Defender for IoT。

3. 转到“更新”页。

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Defender for IoT 的“更新”页的屏幕截图。":::

4. 在“传感器”部分选择“下载”，然后保存文件 。

5. 在传感器控制台的边栏中，选择“系统设置”。

6. 在“版本更新”窗格中，选择“更新”。

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="屏幕截图：升级窗格。":::

7. 选择从 Defender for IoT“更新”页下载的文件。

8. 更新过程随即开始，在此期间系统会重新启动两次。 在第一次重新启动之后（完成更新过程之前），系统中会打开登录窗口。 登录后，升级版本将显示在边栏的左下方。

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="登录后显示的升级版本的屏幕截图。":::

## <a name="forward-sensor-failure-alerts"></a>转发传感器失败警报

可将警报转发到第三方，以提供以下方面的详细信息：

- 已断开连接的传感器

- 远程备份失败

当你为系统通知创建转发规则时，将发送此信息。

> [!NOTE]
> 管理员可以发送系统通知。

若要发送通知，请执行以下操作：

1. 登录到本地管理控制台。
1. 在边侧菜单中选择“转发”。
1. 创建一个转发规则。
1. 选择“报告系统通知”。

有关转发规则的详细信息，请参阅[转发警报信息](how-to-forward-alert-information-to-partners.md)。

## <a name="adjust-system-properties"></a>调整系统属性

系统属性控制传感器中的各种操作和设置。 编辑或修改这些属性可能会给传感器控制台操作造成不利影响。

在更改设置之前，请咨询 [Microsoft 支持人员](https://support.microsoft.com/)。

若要访问系统属性，请执行以下操作：

1. 登录到本地管理控制台或传感器。

2. 选择“系统设置”。

3. 在“常规”部分选择“系统属性” 。

## <a name="see-also"></a>另请参阅

[威胁情报研究和包](how-to-work-with-threat-intelligence-packages.md)

[通过管理控制台管理传感器](how-to-manage-sensors-from-the-on-premises-management-console.md)