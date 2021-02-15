---
title: 管理单个传感器
description: 了解如何管理单个传感器，包括管理激活文件、执行备份和更新独立传感器。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 02/02/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: ba98eb7e87ba277dcd5279ecf17373a8276b1cb1
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523968"
---
# <a name="manage-individual-sensors"></a>管理单个传感器

本文介绍如何管理单个传感器。 任务包括管理激活文件、执行备份和更新独立传感器。

你还可以在本地管理控制台中执行某些传感器管理任务，可以同时管理多个传感器。

您可以使用 Azure 门户进行传感器载入和注册。

## <a name="manage-sensor-activation-files"></a>管理传感器激活文件

你的传感器已通过 Azure 门户的 IoT 用于 IoT 的 Azure Defender。 每个传感器都载入为本地连接的传感器或连接了云的传感器。

将唯一的激活文件上传到部署的每个传感器。 有关何时以及如何使用新文件的详细信息，请参阅 [上传新的激活文件](#upload-new-activation-files)。 如果无法上传文件，请参阅 [排查激活文件上传问题](#troubleshoot-activation-file-upload)。

### <a name="about-activation-files-for-locally-connected-sensors"></a>关于本地连接的传感器的激活文件

本地连接的传感器与 Azure 订阅相关联。 本地连接的传感器的激活文件包含到期日期。 此日期前一个月，传感器控制台的顶部会出现一条警告消息。 在更新激活文件之前，将一直保留该警告。

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="系统设置的屏幕截图。":::

即使激活文件已过期，你也可以继续使用适用于 IoT 功能的 Defender。 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>关于云连接传感器的激活文件

云连接的传感器与 IoT 中心的 Defender 关联。 这些传感器不受激活文件的时间段的限制。 用于云连接的传感器的激活文件用于确保连接到 IoT 中心的 Defender。

### <a name="upload-new-activation-files"></a>上传新激活文件

在以下情况时，你可能需要为载入传感器上载新的激活文件：

- 激活文件在本地连接的传感器上过期。 

- 你希望在不同的传感器管理模式下工作。 

- 需要为 IoT 中心分配一个云连接传感器。

添加新的激活文件：

1. 请参阅 **传感器管理** 页面。

2. 选择要为其上传新的激活文件的传感器。

3. 删除它。

4. 在新模式下，或使用新的 IoT 中心的 Defender，从载入页中再次 **载入** 传感器。

5. 从 **下载激活文件** 页下载激活文件。

6. 保存文件。

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="从用于 IoT 中心的 Defender 下载激活文件。":::

7. 登录到 "用于 IoT 的 Defender 传感器" 控制台。

8. 在传感器控制台中，选择 "**系统设置** 重新  >  **激活**"。

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="&quot;系统设置&quot; 屏幕上的重新激活选项。":::

9. 选择 " **上传** "，然后选择保存的文件。

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="上载保存的文件。":::

10. 选择“激活”  。

### <a name="troubleshoot-activation-file-upload"></a>排查激活文件上传问题

如果无法上载激活文件，您将收到一条错误消息。 可能发生了以下事件：

- **对于本地连接的传感器**：激活文件无效。 如果文件无效，请前往 IoT 门户的 Defender。 在 " **传感器管理** " 页上，选择具有无效文件的传感器，并下载新的激活文件。

- **对于已连接到云的传感器**：传感器无法连接到 internet。 查看传感器的网络配置。 如果你的传感器需要通过 web 代理进行连接以访问 internet，请验证是否已在 **传感器网络配置** 屏幕上正确配置代理服务器。 验证是否 \* 允许在防火墙和/或代理中使用 azure-devices.net:443。 如果不支持通配符，或者需要更多控制，则应在防火墙和/或代理中打开用于 IoT 中心的特定 Defender 的 FQDN。 有关详细信息，请参阅 [Reference-IoT 中心终结点](../iot-hub/iot-hub-devguide-endpoints.md)。  

- **对于连接了云的传感器**：激活文件有效，但 IoT 拒绝了该激活。 如果你无法解决此问题，你可以从 IoT 门户的 Defender 的 "站点和传感器" 页下载另一激活。 如果这不起作用，请联系 Microsoft 支持部门。

## <a name="manage-certificates"></a>管理证书

安装传感器后，将生成本地自签名证书，并使用该证书访问传感器 web 应用程序。 首次登录到传感器时，系统将提示管理员用户提供 SSL/TLS 证书。  有关首次设置的详细信息，请参阅 [登录和激活传感器](how-to-activate-and-set-up-your-sensor.md)。

本文提供有关更新证书、使用证书 CLI 命令以及支持的证书和证书参数的信息。

### <a name="about-certificates"></a>关于证书

Azure Defender for IoT 使用 SSL/TLS 证书来执行以下操作：

- 通过上传 CA 签名证书来满足组织要求的特定证书和加密要求。

- 允许在管理控制台和连接的传感器之间以及管理控制台和高可用性管理控制台之间进行验证。 对照证书吊销列表和证书到期日期对验证进行评估。 *如果验证失败，则会停止管理控制台和传感器之间的通信，并在控制台中显示验证错误*。 默认情况下，安装后会启用此选项。

 第三方转发规则（例如发送到 SYSLOG、Splunk 或 ServiceNow 的警报信息）或不验证与 Active Directory 的通信。

#### <a name="ssl-certificates"></a>SSL 证书数

用于 IoT 传感器和本地管理控制台的 Defender 用于以下功能的 SSL 和 TLS 证书： 

 - 保护用户之间的通信和设备的 web 控制台。 
 
 - 保护与传感器和本地管理控制台上的 REST API 的通信。
 
 - 保护传感器与本地管理控制台之间的通信。 

安装完成后，设备会生成一个本地自签名证书，以允许对 web 控制台进行初步访问。 可以使用命令行工具安装 Enterprise SSL 和 TLS 证书 [`cyberx-xsense-certificate-import`](#cli-commands) 。

 > [!NOTE]
 > 对于集成和转发规则（其中设备是会话的客户端和发起方），会使用特定证书，并且与系统证书无关。  
 >
 >在这些情况下，通常会从服务器接收证书，或使用非对称加密，其中提供了特定的证书来设置集成。

设备可能使用唯一的证书文件。 如果需要替换证书，你已上传;

- 在版本10.0 中，可以从 "系统设置" 菜单替换证书。

- 对于早于10.0 的版本，可以使用命令行工具替换 SSL 证书。

### <a name="update-certificates"></a>更新证书

传感器管理员用户可以更新证书。

若要更新证书：  

1. 选择 " **系统设置**"。

1. 选择 " **SSL/TLS 证书"。**
1. 删除或编辑证书，然后添加一个新证书。

    - 添加证书名称。
    
    - 上传 CRT 文件和密钥文件并输入通行短语。
    - 如有必要，请上传 PEM 文件。

若要更改验证设置：

1. 启用或禁用 " **启用证书验证** " 切换。

1. 选择“保存” 。

如果启用了该选项并且验证失败，则会停止管理控制台和传感器之间的通信，并在控制台中显示验证错误。

### <a name="certificate-support"></a>证书支持

支持以下证书：

- 专用 PKI 和企业密钥基础结构 (专用 PKI) 

- 公共密钥基础结构 (公共 PKI)  

- 在设备上本地生成 (本地自签名) 。 

> [!IMPORTANT]
> 不建议使用自签名证书。 这种类型的连接是不安全的，只应用于测试环境。 由于无法验证证书的所有者，并且无法维护系统的安全，因此不应在生产网络中使用自签名证书。

### <a name="supported-ssl-certificates"></a>支持的 SSL 证书 

支持以下参数。 

**证书 CRT**

- 域名的主证书文件

- 签名算法 = SHA256RSA
- 签名哈希算法 = SHA256
- 有效起始日期 = 有效的过去日期
- 有效截止日期 = 有效的将来日期
- 公钥 = (最小) 或4096位的 RSA 2048 位
- CRL 分发点 = 指向 .crl 文件的 URL
- Subject CN = URL，可以是通配符证书;例如，"contoso"。 <span>com 或 * .com。 <span>com
- 主题 (C) ountry = 已定义，例如 US
- 主题 (OU) Org Unit = 已定义，例如 Contoso 实验室
- 主题 (O) 组织 = 已定义，例如 Contoso Inc.。

**密钥文件**

- 创建 CSR 时生成的密钥文件。

- RSA 2048 位 (最小) 或4096位。

 > [!Note]
 > 使用4096bits 的密钥长度：
 > - 每次连接开始时的 SSL 握手都将变慢。  
 > - 握手期间的 CPU 使用率增加。 

**证书链**

- 如果 CA 提供的任何) ，中间证书文件 (

- 颁发服务器证书的 CA 证书应位于文件中，然后是所有其他证书，然后是根。 
- 可以包含包属性。

**密码**

- 支持一个密钥。

- 在导入证书时进行设置。

具有其他参数的证书可能有效，但 Microsoft 不支持。

#### <a name="encryption-key-artifacts"></a>加密密钥项目

**pem-证书容器文件**

隐私增强邮件 (PEM) 文件是用于保护电子邮件的常规文件类型。 如今、PEM 文件与证书一起使用，并使用 x509 ASN1 项。  

此容器文件在 Rfc 1421 到1424中定义，这种容器格式只能包括公共证书。 例如，Apache 安装，CA 证书，文件，等等，SSL 或证书。 这可以包括整个证书链，包括公钥、私钥和根证书。  

它还可以将 CSR 编码为 PKCS10 格式，可将其转换为 PEM。

**cert .cer-证书容器文件**

`.pem` `.der` 具有不同扩展名的或带格式的文件。 Windows 资源管理器将文件识别为证书。 `.pem`   Windows 资源管理器不能识别该文件。

**。 key –私钥文件**

密钥文件的格式与 PEM 文件的格式相同，但它具有不同的扩展名。

#### <a name="additional-commonly-available-key-artifacts"></a>其他常用的关键项目

**。 csr-证书签名请求**。  

此文件用于提交到证书颁发机构。 实际格式为 PKCS10，它是在 RFC 2986 中定义的，并且可能包括请求的证书的部分或全部重要详细信息。 例如，使用者、组织和省/市/自治区。 它是证书的公钥，由 CA 签名，并接收返回的证书。  

返回的证书是公共证书，其中包括公钥，而不包括私钥。 

**pkcs12 – password 容器**。 

最初由 RSA 在 (PKCS) 的 Public-Key 加密标准中定义，这是由 Microsoft 最初增强的 12-变体，并将在以后提交为 RFC 7292。  

此容器格式需要包含公用和专用证书对的密码。 与 `.pem`   文件不同，此容器是完全加密的。  

可以使用 OpenSSL 将其转换为 `.pem`   具有公钥和私钥的文件： `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**der –二进制编码的 PEM**。

以二进制格式对 node.js 语法进行编码的方式是通过 `.pem`   文件，该文件只是一个 Base64 编码的 `.der` 文件。 

OpenSSL 可以将这些文件转换为 `.pem` ：  `openssl x509 -inform der -in to-convert.der -out converted.pem` 。  

Windows 会将这些文件识别为证书文件。 默认情况下，Windows 会将证书导出为 `.der` 具有不同扩展名的格式化文件。  

**.crl-证书吊销列表**。  
证书颁发机构会生成这些文件，以便在证书过期前取消授权证书。
 
##### <a name="cli-commands"></a>CLI 命令

使用 `cyberx-xsense-certificate-import` CLI 命令导入证书。 若要使用此工具，需要使用 WinSCP 或 Wget 等工具将证书文件上传到设备。

此命令支持以下输入标志：

- `-h`：显示命令行帮助语法。

- `--crt`：指向)  ( 的证书文件的路径。

- `--key`：  \* 密钥文件。 密钥长度应至少为2048位。

- `--chain`：证书链文件的路径 (可选) 。

- `--pass`：用于加密证书的密码 (可选) 。

- `--passphrase-set`：默认值 = `False` 、未使用的。 设置为 `True` 以使用上一个证书提供的以前的密码 (可选) 。

使用 CLI 命令时：

- 验证证书文件在设备上是否可读。

- 验证证书中的域名和 IP 是否与 IT 部门计划的配置相匹配。

### <a name="use-openssl-to-manage-certificates"></a>使用 OpenSSL 管理证书

通过以下命令管理证书：

| 说明 | CLI 命令 |
|--|--|
| 生成新的私钥和证书签名请求 | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| 生成自签名证书 | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| 为现有私钥 (CSR) 生成证书签名请求 | `openssl req -out CSR.csr -key privateKey.key -new` |
| 基于现有证书生成证书签名请求 | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| 从私钥中删除密码 | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

如果需要检查证书、CSR 或私钥中的信息，请使用以下命令：

| 说明 | CLI 命令 |
|--|--|
|  (CSR 检查证书签名请求)  | `openssl req -text -noout -verify -in CSR.csr` |
| 检查私钥 | `openssl rsa -in privateKey.key -check` |
| 检查证书 | `openssl x509 -in certificate.crt -text -noout`  |

如果收到一条错误消息，指出私钥与证书不符，或者你安装到站点的证书不受信任，请使用以下命令来修复此错误：

| 说明 | CLI 命令 |
|--|--|
| 检查公钥的 MD5 哈希，以确保它与 CSR 或私钥中的内容相匹配 | 2. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> pps-2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

若要将证书和密钥转换为不同的格式，以使它们与特定类型的服务器（或软件）兼容，请使用以下命令：

| 说明 | CLI 命令 |
|--|--|
| 将 DER 文件 () 转换为 PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| 将 PEM 文件转换为 DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| 将包含私钥和证书的 PKCS # 12 文件 () 转换为 PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />你可以 `-nocerts` 仅添加到输出私钥，或添加 `-nokeys` 以仅输出证书。 |
| 将 PEM 证书文件和私钥转换为 PKCS # 12 ()  | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="connect-a-sensor-to-the-management-console"></a>将传感器连接到管理控制台

本部分介绍如何确保传感器与本地管理控制台之间的连接。 如果使用的是有气流的网络，并且想要从传感器将设备和警报信息发送到管理控制台，则需要执行此操作。 此连接还允许管理控制台将系统设置推送到传感器，并在传感器上执行其他管理任务。

若要连接：

1. 登录到本地管理控制台。

2. 选择 " **系统设置**"。

3. 在 " **传感器设置-连接字符串** " 部分中，复制自动生成的连接字符串。

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="从此屏幕复制连接字符串。"::: 

4. 登录到传感器控制台。

5. 在左侧窗格中，选择 " **系统设置**"。

6. 选择 " **管理控制台连接**"。

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="&quot;管理控制台连接&quot; 对话框的屏幕截图。":::

7. 将连接字符串粘贴到 " **连接字符串** " 框中，然后选择 " **连接**"。

8. 在本地管理控制台中，在 " **站点管理** " 窗口中，将传感器分配到一个区域。

## <a name="change-the-name-of-a-sensor"></a>更改传感器的名称

可以更改传感器控制台的名称。 新名称将显示在控制台 web 浏览器、各种控制台窗口和疑难解答日志中。

对于本地连接的传感器和连接到云的传感器，更改传感器名称的过程会有所不同。 默认名称为 " **传感器**"。

### <a name="change-the-name-of-a-locally-connected-sensor"></a>更改本地连接的传感器的名称

若要更改名称：

1. 在控制台左窗格的底部，选择 "当前传感器" 标签。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="显示传感器标签的屏幕截图。":::

1. 在 " **编辑传感器名称** " 对话框中，输入名称。

1. 选择“保存” 。 新名称将被应用。

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>更改已连接到云的传感器的名称

如果传感器注册为云连接传感器，则传感器名称由注册过程中分配的名称定义。 此名称包含在首次登录时上载的激活文件中。 若要更改传感器的名称，需要上载新的激活文件。

若要更改名称：

1. 在 Azure Defender for IoT 门户中，请参阅站点和传感器页面。

1. 从 "站点和传感器" 页中删除该传感器。

1. 通过从 "入门" 页中选择 " **板载传感器** "，以新名称注册。

1. 下载新的激活文件。

1. 登录到 "用于 IoT 的 Defender 传感器" 控制台。

1. 在传感器控制台中，选择“系统设置”，然后选择“重新激活”。

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="上传激活文件以重新激活传感器。":::

1. 选择“上传”并选择已保存的文件。

1. 选择“激活”  。

## <a name="update-the-sensor-network-configuration"></a>更新传感器网络配置

传感器网络配置是在安装传感器的过程中定义的。 您可以更改配置参数。 还可以设置代理配置。

如果创建新的 IP 地址，则可能需要重新登录。

更改配置：

1. 在侧边菜单中，选择 " **系统设置**"。

2. 在 " **系统设置** " 窗口中，选择 " **网络**"。

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

4. 选择“保存” 。

## <a name="synchronize-time-zones-on-the-sensor"></a>同步传感器上的时区

你可以配置传感器的时间和区域，以便所有用户看到相同的时间和区域。

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="配置时间和区域。":::

| 参数 | 说明 |
|--|--|
| 时区 | 的时区定义：<br />-警报<br />-趋势和统计小组件<br />-数据挖掘报表<br />   -风险评估报表<br />-攻击向量 |
| 日期格式 | 选择以下格式选项之一：<br />-dd/MM/yyyy HH： MM： ss<br />-MM/dd/yyyy HH： MM： ss<br />-yyyy/MM/dd HH： MM： ss |
| 日期和时间 | 以所选格式显示当前日期和本地时间。<br />例如，如果你的实际位置是 "美国" 和 "纽约"，但时区设置为 "欧洲" 和 "柏林"，则根据柏林当地时间显示时间。 |

若要配置传感器时间：

1. 在侧边菜单中，选择 " **系统设置**"。

2. 在 " **系统设置** " 窗口中，选择 " **Time & 地区性**"。

3. 设置参数，然后选择 " **保存**"。

## <a name="set-up-backup-and-restore-files"></a>设置备份和还原文件

系统备份会在每天凌晨3:00 自动执行。 数据将保存在传感器的其他磁盘上。 默认位置为 `/var/cyberx/backups`。

可以自动将此文件传输到内部网络。

> [!NOTE]
> - 备份和还原过程只能在相同版本之间执行。
> - 在某些体系结构中，备份处于禁用状态。 可以在文件中启用它 `/var/cyberx/properties/backup.properties` 。

使用本地管理控制台控制传感器时，可以使用传感器的备份计划来收集这些备份，并将其存储在管理控制台或外部备份服务器上。

**备份的内容：** 配置和数据。

**不备份的内容：** PCAP 文件和日志。 可以手动备份和还原 PCAPs 和日志。

传感器备份文件通过以下格式自动命名： `<sensor name>-backup-version-<version>-<date>.tar` 。 例如 `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`。

配置备份：

- 登录到管理帐户，然后输入 `$ sudo cyberx-xsense-system-backup` 。

还原最新备份文件：

- 登录到管理帐户，然后输入 `$ sudo cyberx-xsense-system-restore` 。

若要将备份保存到外部 SMB 服务器：

1. 在外部 SMB 服务器中创建共享文件夹。

    获取访问 SMB 服务器所需的文件夹路径、用户名和密码。

2. 在传感器中，为备份创建目录：

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. 编辑 `fstab`：

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. 编辑并创建 SMB 服务器共享的凭据：

    `sudo nano /etc/samba/user` 

5. 添加：

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. 装载目录：

    `sudo mount -a`

7. 为 IoT 传感器的 Defender 上的共享文件夹配置备份目录：  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>还原传感器

可以通过使用 CLI 还原备份。

**从控制台还原：**

- 从传感器的 "**系统设置**" 窗口中选择 "**还原映像**"。

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="通过选择 &quot;还原映像&quot; 按钮。":::

控制台将显示还原失败。

**使用 CLI 还原：**

- 登录到管理帐户，然后输入 `$ sudo cyberx-management-system-restore` 。

## <a name="update-a-standalone-sensor-version"></a>更新独立传感器版本

以下过程描述如何使用传感器控制台更新独立传感器。 更新过程大约需30分钟。

1. 转到 [Azure 门户](https://portal.azure.com/)。

2. 中转到用于 IoT 的 Defender。

3. 请参阅 " **更新** " 页面。

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="用于 IoT 的 Defender 的 &quot;更新&quot; 页的屏幕截图。":::

4. 从 "**传感器**" 部分选择 "**下载**" 并保存文件。

5. 在传感器控制台的边栏中，选择 " **系统设置**"。

6. 在 " **版本升级** " 窗格上，选择 " **升级**"。

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="&quot;升级&quot; 窗格的屏幕截图。":::

7. 选择从 "用于 IoT **更新** 的 Defender" 页面下载的文件。

8. 更新过程开始，在这段时间内系统重新启动两次。 第一次重新启动之后 (在更新过程) 完成之前，系统将打开并出现登录窗口。 登录后，升级版本将出现在侧栏的左下角。

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="登录后显示的升级版本的屏幕截图。":::

## <a name="forward-sensor-failure-alerts"></a>转发传感器故障警报

你可以将警报转发给第三方，以提供有关以下方面的详细信息：

- 断开连接传感器

- 远程备份失败

当你为系统通知创建转发规则时，将发送此信息。

> [!NOTE]
> 管理员可以发送系统通知。

发送通知：

1. 登录到本地管理控制台。
1. 从侧边菜单中选择 " **转发** "。
1. 创建转发规则。
1. 选择 " **报表系统通知**"。

有关转发规则的详细信息，请参阅 [转发警报信息](how-to-forward-alert-information-to-partners.md)。

## <a name="adjust-system-properties"></a>调整系统属性

系统属性控制传感器中的各种操作和设置。 编辑或修改它们可能会损坏传感器控制台的操作。

更改设置之前，请参考 [Microsoft 支持部门](https://support.microsoft.com/) 。

访问系统属性：

1. 登录到本地管理控制台或传感器。

2. 选择 " **系统设置**"。

3. 从 "**常规**" 部分中选择 "**系统属性**"。

## <a name="next-steps"></a>后续步骤

[威胁情报研究和包](how-to-work-with-threat-intelligence-packages.md)

[通过管理控制台管理传感器](how-to-manage-sensors-from-the-on-premises-management-console.md)