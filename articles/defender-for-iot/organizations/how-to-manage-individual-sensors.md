---
title: 管理单个传感器
description: 了解如何管理单个传感器，包括管理激活文件、执行备份和更新独立传感器。
ms.date: 05/26/2021
ms.topic: how-to
ms.openlocfilehash: 7c54c2eef98cdf3c68f4c03f09f95972d57aba71
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745408"
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

若要添加新的激活文件，请执行以下操作：

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

安装传感器后，将生成一个本地自签名证书，该证书用于访问传感器 Web 应用程序。 首次登录到传感器时，系统会提示管理员用户提供 SSL/TLS 证书。  有关首次设置的详细信息，请参阅[登录和激活传感器](how-to-activate-and-set-up-your-sensor.md)。

此文提供了有关更新证书、使用证书 CLI 命令以及支持的证书和证书参数的信息。

### <a name="about-certificates"></a>关于证书

Azure Defender for IoT 使用 SSL/TLS 证书来实现以下目的：

- 通过上传 CA 签名的证书来满足组织请求的特定证书和加密要求。

- 允许在管理控制台与连接的传感器之间以及在管理控制台与高可用性管理控制台之间进行验证。 根据证书吊销列表 (CRL) 和证书到期日期对验证进行评估。 如果验证失败，则管理控制台和传感器之间的通信将停止，且控制台中会出现验证错误。 安装后默认启用此选项。

- 将验证第三方转发规则（例如，发送到 SYSLOG、Splunk 或 ServiceNow 的警报信息）或者与 Active Directory 的通信。

### <a name="about-crl-servers"></a>关于 CRL 服务器

验证启用时，设备应该能够与证书定义的 CRL 服务器建立连接。 默认情况下，证书将引用 HTTP 端口 80 上的 CRL URL。 某些组织安全策略可能会阻止对此端口的访问。 如果组织无法访问端口 80，可以：
1. 定义证书中的另一个 URL 和特定端口。 
- URL 应定义为 http://，而不是 https://。
- 验证目标 CRL 服务器能否侦听定义的端口。 
1. 使用将访问端口 80 上的 CRL 的代理服务器。
1. 不执行 CRL 验证。 在这种情况下，请删除证书中的 CRL URL 引用。

### <a name="about-ssltls-certificates"></a>关于 SSL/TLS 证书

Defender for IoT 传感器和本地管理控制台将 SSL 和 TLS 证书用于以下功能：

 - 保护用户与设备的 Web 控制台之间的通信。
 
 - 保护与传感器和本地管理控制台上的 REST API 的通信。
 
 - 保护传感器与本地管理控制台之间的通信。

安装后，设备会生成一个本地自签名证书，用于对 Web 控制台进行初步访问。

 > [!NOTE]
 > 对于设备是会话客户端和发起端的集成与转发规则，将使用特定的证书，并且这些证书与系统证书无关。  
 >
 >在这种情况下，证书通常是从服务器接收的，或使用非对称加密，在此加密模式下，将提供特定的证书来设置集成。

设备可以使用唯一的证书文件。 如果你需要替换自己上传的证书：

- 从版本 10.0 开始，可以从“系统设置”菜单替换证书。

- 对于 10.0 之前的版本，可以使用命令行工具替换 SSL 证书。

### <a name="update-certificates"></a>更新证书

传感器管理员用户可以更新证书。

若要更新证书，请执行以下操作：  

1. 选择“系统设置”。

1. 选择“SSL/TLS 证书”。
1. 删除或编辑证书，然后添加一个新证书。

    - 添加证书名称。
    
    - 上传 CRT 文件和密钥文件，然后输入通行短语。
    - 如有必要，请上传 PEM 文件。

若要更改验证设置，请执行以下操作：

1. 启用或禁用“启用证书验证”切换开关。

1. 选择“保存”。

如果已启用该选项但验证失败，则管理控制台与传感器之间的通信将会停止，并且控制台中将出现验证错误。

### <a name="certificate-support"></a>证书支持

支持以下证书：

- 专用和企业密钥基础结构（专用 PKI）

- 公钥基础结构（公共 PKI） 

- 在设备上本地生成（本地自签名）。 

> [!IMPORTANT]
> 不建议使用自签名证书。 此类型的连接不安全，应仅用于测试环境。 由于无法验证证书的所有者，也无法维护系统的安全，因此切勿将自签名证书用于生产网络。

### <a name="supported-ssl-certificates"></a>支持的 SSL 证书 

支持以下参数。 

证书 CRT

- 域名的主证书文件

- 签名算法 = SHA256RSA
- 签名哈希算法 = SHA256
- 有效起始日期 = 有效的过去日期
- 有效结束日期 = 有效的将来日期
- 公钥 = RSA 2048 位（最小）或 4096 位
- CRL 分发点 = 指向 .crl 文件的 URL
- 使用者 CN = URL，可以是通配符证书；例如，Sensor.contoso.<span>com 或 *.contoso.<span>com
- 使用者国家/地区 (C) = 定义的值，例如 US
- 使用者组织单位 (OU) = 定义的值，例如 Contoso Labs
- 使用者组织 (O) = 定义的值，例如 Contoso Inc.

密钥文件

- 创建 CSR 时生成的密钥文件。

- RSA 2048 位（最小）或 4096 位。

 > [!Note]
 > 使用 4096 位密钥长度：
 > - 每次连接开始时的 SSL 握手都将变慢。  
 > - 握手期间的 CPU 使用率有所增加。 

证书链

- CA 提供的中间证书文件（如有）

- 颁发服务器证书的 CA 证书应包含在该文件中的最前面，其后面是任何其他证书，最后是根证书。 
- 可以包含 Bag 特性。

**密码**

- 支持一个密钥。

- 在导入证书时进行设置。

包含其他参数的证书可能有效，但 Microsoft 不支持它们。

#### <a name="encryption-key-artifacts"></a>加密密钥项目

.pem – 证书容器文件

隐私增强邮件 (PEM) 文件是用于保护电子邮件的常规文件类型。 如今，PEM 文件与证书一起使用，并使用 x509 ASN1 密钥。  

容器文件在 RFC 1421 到 1424（只能包括公共证书的容器格式）中定义。 例如，Apache 安装、CA 证书、文件、ETC、SSL 或 CERTS。 这可以包含整个证书链，包括公钥、私钥和根证书。  

还可以将 CSR 编码为 PKCS10 格式，可将其转换为 PEM。

c.cert .cer .crt – 证书容器文件

具有不同扩展名的 `.pem` 或 `.der` 格式的文件。 Windows 资源管理器将该文件识别为证书。 Windows 资源管理器无法识别 `.pem` 文件。

.key – 私钥文件

密钥文件的格式与 PEM 文件的格式相同，但它具有不同的扩展名。

#### <a name="other-commonly-available-key-artifacts"></a>其他常用的密钥项目

.csr  - 证书签名请求。  

此文件用于提交到证书颁发机构。 实际格式为 PKCS10，它是在 RFC 2986 中定义的，并且可能包含请求的证书的部分或全部密钥详细信息。 例如，使用者、组织和状态。 它是证书的公钥，由 CA 签名，并接收返回的证书。  

返回的证书是公共证书，其中包含公钥，而不包含私钥。 

.pkcs12 .pfx .p12  – 密码容器。 

最初由 RSA 在公钥加密标准 (PKCS) 中定义，12-变体最初由 Microsoft 增强，以后提交为 RFC 7292。  

此容器格式需要包含公共证书对和专用证书对的密码。 与 `.pem` 文件不同，此容器是完全加密的。  

可以使用 OpenSSL 将此文件转换为包含公钥和私钥的 `.pem` 文件： `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

.der – 二进制编码的 PEM。

以二进制格式对 ASN.1 语法进行编码的方式是通过 `.pem` 文件，该文件只是 Base64 编码的 `.der` 文件。 

OpenSSL 可以将这些文件转换为 `.pem`: `openssl x509 -inform der -in to-convert.der -out converted.pem`。  

Windows 会将这些文件识别为证书文件。 默认情况下，Windows 会将证书导出为具有不同扩展名的 `.der` 格式化文件。  

.crl  - 证书吊销列表。  
证书颁发机构生成这些证书，作为在证书过期前取消授权证书的方式。
 
##### <a name="cli-commands"></a>CLI 命令

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