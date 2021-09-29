---
title: 管理本地管理控制台
description: 了解本地管理控制台选项，如备份和还原、定义主机名和设置传感器的代理。
ms.date: 1/12/2021
ms.topic: article
ms.openlocfilehash: 9b956dbfa640862d10d0e47aef97e5a1727052c7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625214"
---
# <a name="manage-the-on-premises-management-console"></a>管理本地管理控制台

本文介绍本地管理控制台选项，如备份和还原、下载委员会设备激活文件、更新证书以及设置传感器的代理。

通过 Azure 门户载入本地管理控制台。

## <a name="upload-an-activation-file"></a>上传激活文件

首次登录后，将下载本地管理控制台的激活文件。 此文件包含在载入过程中定义的聚合提交设备。 此列表包括与多个订阅关联的传感器。

初始激活后，受监视的设备数可能会超过在载入过程中定义的已提交设备数。 例如，如果将更多传感器连接到管理控制台，则可能会发生此事件。 如果受监视的设备数与已提交的设备数之间存在差异，则管理控制台中会出现警告。 如果发生此事件，应上传新的激活文件。

**若要上传激活文件，请执行以下操作：**

1. 转到 Azure Defender for IoT“定价”页。
1. 选择“下载管理控制台的激活文件”选项卡 此时将下载激活文件。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="下载激活文件。":::

1. 从管理控制台中选择“系统设置”。
1. 选择“激活”。
1. 选择“选择文件”，然后选择已保存的文件。

## <a name="manage-certificates"></a>管理证书

安装本地管理控制台后，将生成一个本地自签名证书，该证书用于访问 Web 应用程序。 首次登录到本地管理控制台时，系统会提示管理员用户提供 SSL/TLS 证书。 

管理员可能需要更新初始登录后上传的证书。 例如，如果证书已过期，则可能会发生这种情况。

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

## <a name="define-backup-and-restore-settings"></a>定义备份和还原设置

本地管理控制台系统备份会在每天自动执行。 数据保存在不同的磁盘上。 默认位置为 `/var/cyberx/backups`。 

可以自动将此文件传输到内部网络。 

> [!NOTE]
> 只能在同一版本上执行备份和还原过程。 

若要备份本地管理控制台计算机，请执行以下操作： 

- 登录到管理帐户，然后输入 `sudo cyberx-management-backup -full`。

若要还原最新备份文件，请执行以下操作：

- 登录到管理帐户，然后输入 `$ sudo cyberx-management-system-restore`。

若要将备份保存到外部 SMB 服务器，请执行以下操作：

1. 在外部 SMB 服务器中创建一个共享文件夹。  

   获取访问 SMB 服务器所需的文件夹路径、用户名和密码。 

2. 在 Defender for IoT 中，为备份创建目录：

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. 编辑 fstab：  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. 编辑或创建要共享的 SMB 服务器的凭据： 

   - `sudo nano /etc/samba/user` 

5. 添加： 

   - `username=<user name>`

   - `password=<password>` 

6. 装载目录： 

   - `sudo mount -a` 

7. 在 Defender for IoT 本地管理控制台上配置共享文件夹的备份目录：  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>编辑主机名

若要编辑组织 DNS 服务器中配置的管理控制台主机名，请执行以下操作：

1. 在管理控制台的左窗格中，选择“系统设置”。  

2. 在控制台的“网络”部分，选择“网络”。 

3. 输入组织 DNS 服务器中配置的主机名。 

4. 选择“保存”。

## <a name="define-vlan-names"></a>定义 VLAN 名称

VLAN 名称在传感器和管理控制台之间未同步。 在组件上定义相同的名称。

在“网络”区域中，选择“VLAN”并将名称添加到已发现的 VLAN ID。 再选择“保存”。

## <a name="define-a-proxy-to-sensors"></a>定义传感器的代理

通过阻止用户直接登录到传感器来增强系统安全性。 请改用代理隧道，通过单个防火墙规则让用户从本地管理控制台访问传感器。 此增强功能可降低对传感器之外的网络环境进行未经授权的访问的可能性。

在不直接连接到传感器的环境中使用代理。

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="用户。":::

以下过程将传感器连接到本地管理控制台，并在该控制台上启用隧道：

1. 使用管理凭据登录到本地管理控制台设备 CLI。

2. 键入 `sudo cyberx-management-tunnel-enable` 并选择 **Enter**。

4. 键入 `--port 10000` 并选择 **Enter**。

## <a name="adjust-system-properties"></a>调整系统属性

系统属性控制管理控制台中的各种操作和设置。 编辑或修改它们可能会破坏管理控制台的操作。 更改设置前，请咨询 [Microsoft 支持](https://support.microsoft.com)。

若要访问系统属性，请执行以下操作： 

1. 登录到本地管理控制台或传感器。

2. 选择“系统设置”。

3. 从“常规”部分中选择“系统属性”。

## <a name="change-the-name-of-the-on-premises-management-console"></a>更改本地管理控制台的名称

可以更改本地管理控制台的名称。 新名称显示在控制台 Web 浏览器、各种控制台窗口和疑难解答日志中。 默认名称是“管理控制台”。

若要更改名称：

1. 在左窗格的底部，选择当前名称。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="本地管理控制台版本的屏幕截图。":::

2. 在“编辑管理控制台配置”对话框中，输入新名称。 名称不得超过 25 个字符。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="编辑 Defender for IoT 平台配置的屏幕截图。":::

3. 选择“保存”。 将应用新名称。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="显示控制台的已更改名称的屏幕截图。":::

## <a name="password-recovery"></a>密码恢复

本地管理控制台的密码恢复与设备连接到的订阅相关联。 如果不知道设备连接到哪个订阅，则无法恢复密码。

重置密码：

1. 转到本地管理控制台的登录页。
1. 选择“密码恢复”。
1. 复制唯一标识符。
1. 转到 Defender for IoT“站点和传感器”页，然后选择“恢复我的密码”选项卡。
1. 输入唯一标识符，然后选择“恢复”。 此时将下载激活文件。
1. 转到“密码恢复”页，然后上传激活文件。
1. 选择“**下一页**”。
 
   现在提供了用户名和系统生成的新密码。

> [!NOTE]
> 传感器链接到它最初连接到的订阅。 只能使用其连接到的订阅来恢复密码。

## <a name="update-the-software-version"></a>更新软件版本

以下过程介绍如何更新本地管理控制台软件版本。 更新过程大约需要 30 分钟。

如果使用本地管理控制台和托管传感器，请先更新管理控制台。

1. 转到 [Azure 门户](https://portal.azure.com/)。

1. 转到 Defender for IoT。

1. 转到“更新”页。

1. 从本地管理控制台部分中选择一个版本。

1. 选择“下载”并保存文件。

1. 登录到本地管理控制台，然后从边栏菜单中选择“系统设置”。

1. 在“版本更新”窗格中，选择“更新”。

1. 选择从 Defender for IoT“更新”页下载的文件。

## <a name="mail-server-settings"></a>邮件服务器设置

为本地管理控制台定义 SMTP 邮件服务器设置。

若要定义，请执行以下操作：

1. 使用管理凭据登录到本地管理的 CLI。
1. 键入 ```nano /var/cyberx/properties/remote-interfaces.properties```。
1. 按 Enter。 将显示以下提示。
   `mail.smtp_server=`
   `mail.port=25`
   `mail.sender=`
1. 输入 SMTP 服务器名称和发件人，然后按 Enter。

## <a name="see-also"></a>另请参阅

[通过管理控制台管理传感器](how-to-manage-sensors-from-the-on-premises-management-console.md)

[管理单个传感器](how-to-manage-individual-sensors.md)
