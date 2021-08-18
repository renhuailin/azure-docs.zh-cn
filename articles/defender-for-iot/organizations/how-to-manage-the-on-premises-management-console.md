---
title: 管理本地管理控制台
description: 了解本地管理控制台选项，如备份和还原、定义主机名和设置传感器的代理。
ms.date: 1/12/2021
ms.topic: article
ms.openlocfilehash: b1853b8c22e576bade68823ae206a48c443eb231
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015641"
---
# <a name="manage-the-on-premises-management-console"></a>管理本地管理控制台

本文介绍本地管理控制台选项，如备份和还原、下载委员会设备激活文件、更新证书以及设置传感器的代理。

通过 Azure 门户载入本地管理控制台。

## <a name="upload-an-activation-file"></a>上传激活文件

首次登录后，将下载本地管理控制台的激活文件。 此文件包含在载入过程中定义的聚合提交设备。 此列表包括与多个订阅关联的传感器。

初始激活后，受监视的设备数可能会超过在载入过程中定义的已提交设备数。 例如，如果将更多传感器连接到管理控制台，则可能会发生此事件。 如果受监视的设备数与已提交的设备数之间存在差异，则管理控制台中会出现警告。 如果发生此事件，应上传新的激活文件。

若要上传激活文件，请执行以下操作：

1. 转到 Azure Defender for IoT“定价”页。
1. 选择“下载管理控制台的激活文件”选项卡 此时将下载激活文件。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="下载激活文件。":::

1. 从管理控制台中选择“系统设置”。
1. 选择“激活”。
1. 选择“选择文件”，然后选择已保存的文件。

## <a name="manage-certificates"></a>管理证书

安装本地管理控制台后，将生成本地自签名证书，该证书用于访问管理控制台的 Web 应用程序。 管理员用户首次登录到管理控制台后，系统会提示他们提供 SSL/TLS 证书。 有关首次安装的详细信息，请参阅[激活并设置本地管理控制台](how-to-activate-and-set-up-your-on-premises-management-console.md)。

以下部分提供有关更新证书、使用证书 CLI 命令以及支持的证书和证书参数的信息。

### <a name="about-certificates"></a>关于证书

Azure Defender for IoT 使用 SSL 和 TLS 证书来执行以下操作：

- 通过上传 CA 签名的证书来满足组织请求的特定证书和加密要求。

- 允许在管理控制台和连接的传感器之间以及在管理控制台和高可用性管理控制台之间进行验证。 根据证书吊销列表和证书到期日期对验证进行评估。 如果验证失败，则管理控制台和传感器之间的通信将停止，并且控制台中将出现验证错误。 安装后默认启用此选项。

   验证为 `ON` 时，设备应该能够与证书定义的 CRL 服务器建立连接。

不会验证第三方转发规则。 示例是发送到 SYSLOG、Splunk 或 ServiceNow 的警报信息；与 Active Directory 通信。

#### <a name="ssl-certificates"></a>SSL 证书数

Defender for IoT 传感器和本地管理控制台将 SSL 和 TLS 证书用于以下功能： 

 - 保护用户与设备的 Web 控制台之间的通信。 
 
 - 保护与传感器和本地管理控制台上的 REST API 的通信。
 
 - 保护传感器与本地管理控制台之间的通信。 

安装后，设备就会生成本地自签名证书，以允许对 Web 控制台进行初步访问。 可以使用 [`cyberx-xsense-certificate-import`](#cli-commands) 命令行工具安装企业 SSL 和 TLS 证书。 

 > [!NOTE]
 > 对于集成和转发规则（即设备是会话的客户端和发起程序），会使用特定证书，并且这些证书与系统证书无关。  
 >
 >在这些情况下，通常会从服务器接收证书，或证书使用非对称加密（即提供特定证书来设置集成）。 

### <a name="update-certificates"></a>更新证书

本地管理控制台的管理员用户可以更新证书。

若要更新证书，请执行以下操作：  

1. 选择“系统设置”。

1. 选择“SSL/TLS 证书”。
1. 删除或编辑证书，然后添加一个新证书。
   
   - 添加证书名称。
   
   - 上传 CRT 文件和密钥文件，然后输入密码。
   - 如有必要，请上传 PEM 文件。

若要更改验证设置，请执行以下操作：

1. 打开或关闭“启用证书验证”切换。

1. 选择“保存”。

如果已启用此选项且验证失败，则管理控制台和传感器之间的通信将停止，并且控制台中将出现验证错误。

### <a name="certificate-support"></a>证书支持

支持以下证书：

- 专用和企业密钥基础结构（专用 PKI）
 
- 公钥基础结构（公共 PKI） 

- 在设备上本地生成（本地自签名） 

  > [!IMPORTANT]
  > 不建议使用自签名证书。 此类型的连接不安全，应仅用于测试环境。 由于无法验证证书的所有者，也无法维护系统的安全，因此切勿将自签名证书用于生产网络。

### <a name="supported-ssl-certificates"></a>支持的 SSL 证书 

支持以下参数： 

**证书 CRT**

- 域名的主证书文件

- 签名算法 = SHA256RSA
- 签名哈希算法 = SHA256
- 有效起始日期 = 有效的过去日期
- 有效结束日期 = 有效的将来日期
- 公钥 = RSA 2048 位（最小）或 4096 位
- CRL 分发点 = 指向 .crl 文件的 URL
- 使用者 CN = URL 可以是通配符证书；例如，Sensor.contoso.<span>com,or *.contoso.<span>com
- 主题 (C) 国家/地区 = 已定义；例如，美国
- 主题 (OU) 组织单位 = 已定义；例如，Contoso 实验室
- 主题 (O) 组织 = 已定义；例如，Contoso Inc

**密钥文件**

- 创建 CSR 时生成的密钥文件

- RSA 2048 位（最小）或 4096 位

 > [!Note]
 > 使用 4096 位密钥长度：
 > - 每次连接开始时的 SSL 握手都将变慢。  
 > - 握手期间的 CPU 使用率有所增加。 

**证书链**

- CA 提供的中间证书文件（如有）。

- 颁发服务器证书的 CA 证书应先位于该文件中，然后位于任何其他文件中，一直到根目录。 
- 链可以包含包属性。

**密码**

- 支持一个密钥。

- 在导入证书时进行设置。

包含其他参数的证书可能有效，但 Microsoft 不支持它们。

#### <a name="encryption-key-artifacts"></a>加密密钥项目

.pem：证书容器文件

隐私增强邮件 (PEM) 文件是用于保护电子邮件的常规文件类型。 如今，PEM 文件与证书一起使用，并使用 x509 ASN1 密钥。  

容器文件在 RFC 1421 到 1424（只能包括公共证书的容器格式）中定义。 例如，Apache 安装、CA 证书、文件、ETC、SSL 或 CERTS。 这可以包含整个证书链，包括公钥、私钥和根证书。  

还可以将 CSR 编码为 PKCS10 格式，可将其转换为 PEM。

c.cert .cer .crt：证书容器文件

具有不同扩展名的 `.pem` 或 `.der` 格式化文件。 Windows 资源管理器将该文件识别为证书。 Windows 资源管理器无法识别 `.pem` 文件。

.key：私钥文件

密钥文件的格式与 PEM 文件的格式相同，但它具有不同的扩展名。 

#### <a name="other-commonly-available-key-artifacts"></a>其他常用的密钥项目

.csr  - 证书签名请求。  

此文件用于提交到证书颁发机构。 实际格式为 PKCS10，它是在 RFC 2986 中定义的，并且可能包含请求的证书的部分或全部密钥详细信息。 例如，使用者、组织和状态。 它是证书的公钥，由 CA 签名，并接收返回的证书。  

返回的证书是公共证书，其中包含公钥，而不包含私钥。 

.pkcs12 .pfx .p12  – 密码容器。 

最初由 RSA 在公钥加密标准 (PKCS) 中定义，12-变体最初由 Microsoft 增强，以后提交为 RFC 7292。  

此容器格式需要包含公共证书对和专用证书对的密码。 与 `.pem` 文件不同，此容器是完全加密的。  

可以使用 OpenSSL 将该文件转换为包含公钥和私钥的 `.pem` 文件： `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

.der – 二进制编码的 PEM。

以二进制格式对 ASN.1 语法进行编码的方式是通过 `.pem` 文件，该文件只是 Base64 编码的 `.der` 文件。 

OpenSSL 可以将这些文件转换为 `.pem`: `openssl x509 -inform der -in to-convert.der -out converted.pem`。  

Windows 会将这些文件识别为证书文件。 默认情况下，Windows 会将证书导出为具有不同扩展名的 `.der` 格式化文件。

.crl  - 证书吊销列表。  

证书颁发机构生成这些证书，作为在证书过期前取消授权证书的方式。 

#### <a name="cli-commands"></a>CLI 命令

使用 `cyberx-xsense-certificate-import` CLI 命令来导入证书。 若要使用此工具，需要使用 WinSCP 或 Wget 等工具将证书文件上传到设备。

此命令支持以下输入标志：

- `-h`：显示命令行帮助语法。

- `--crt`：指向证书文件的路径（.crt 扩展名）。

- `--key`：\*.key 文件。 密钥长度应至少为 2,048 位。

- `--chain`：证书链文件的路径（可选）。

- `--pass`：用于加密证书的密码（可选）。

- `--passphrase-set`：默认值 = `False`，未使用。 设置为 `True` 以使用上一个证书提供的先前密码（可选）。

使用 CLI 命令时：

- 验证证书文件在设备上是否可读。

- 验证证书中的域名和 IP 是否与 IT 部门计划的配置匹配。

### <a name="use-openssl-to-manage-certificates"></a>使用 OpenSSL 管理证书

使用以下命令管理证书：

| 说明 | CLI 命令 |
|--|--|
| 生成新的私钥和证书签名请求 | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| 生成自签名证书 | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| 为现有私钥生成证书签名请求 (CSR) | `openssl req -out CSR.csr -key privateKey.key -new` |
| 基于现有证书生成证书签名请求 | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| 从私钥中删除通行短语 | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

如果需要检查证书、CSR 或私钥中的信息，请使用以下命令：

| 说明 | CLI 命令 |
|--|--|
| 检查证书签名请求 (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| 检查私钥 | `openssl rsa -in privateKey.key -check` |
| 检查证书 | `openssl x509 -in certificate.crt -text -noout`  |

如果收到指示私钥与证书不匹配或安装到站点的证书不受信任的错误，请使用以下命令来修复此错误：

| 说明 | CLI 命令 |
|--|--|
| 检查公钥的 MD5 哈希，以确保它与 CSR 或私钥中的内容匹配 | 1. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

若要将证书和密钥转换为不同的格式，以使它们与特定类型的服务器（或软件）兼容，请使用以下命令：

| 说明 | CLI 命令 |
|--|--|
| 将 DER 文件 (.crt .cer .der) 转换为 PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| 将 PEM 文件转换为 DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| 将包含私钥和证书的 PKCS#12 文件 (.pfx .p12) 转换为 PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />可以添加 `-nocerts` 以仅输出私钥，或添加 `-nokeys` 以仅输出证书。 |
| 将 PEM 证书文件和私钥转换为 PKCS#12 (.pfx .p12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="define-backup-and-restore-settings"></a>定义备份和还原设置

本地管理控制台系统备份会在每天自动执行。 数据保存在不同的磁盘上。 默认位置为 `/var/cyberx/backups`。 

可以自动将此文件传输到内部网络。 

> [!NOTE]
> 只能在同一版本上执行备份和还原过程。 

若要备份本地管理控制台计算机，请执行以下操作： 

- 登录到管理帐户，然后输入 `sudo cyberx-management-backup -full`。

若要还原最新备份文件，请执行以下操作：

- 登录到管理帐户并输入 `$ sudo cyberx-management-system-restore`。

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
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. 输入 SMTP 服务器名称和发件人，然后按 Enter。

## <a name="see-also"></a>另请参阅

[通过管理控制台管理传感器](how-to-manage-sensors-from-the-on-premises-management-console.md)

[管理单个传感器](how-to-manage-individual-sensors.md)
