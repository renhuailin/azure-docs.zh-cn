---
title: 在 Azure Stack Edge Pro GPU 上使用证书 | Microsoft Docs
description: 描述如何在 Azure Stack Edge Pro GPU 设备上使用证书，包括使用原因、证书类型以及在设备上上传证书的方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 534870e6bd67b7aa5273289f3154a794a2b9bd22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519004"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上使用证书

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍可在 Azure Stack Edge Pro 设备上安装的证书类型。 本文还包括每种证书类型的详细信息，以及安装和识别到期日期的过程。  

## <a name="about-certificates"></a>关于证书

证书在公钥和实体（例如域名）之间提供链接，由受信任的第三方（如证书颁发机构）签署（验证）。  证书提供了一种方便的方法来分发受信任的公共加密密钥。 因此，证书确保通信受到信任，并确保你正在向正确的服务器发送加密信息。 

最初配置 Azure Stack Edge Pro 设备时，将自动生成自签名证书。 你也可以选择自带证书。 如果你打算自带证书，则需要遵循一些准则。

## <a name="types-of-certificates"></a>证书类型

在 Azure Stack Edge Pro 设备上使用的各种类型的证书如下： 
- 签名证书
    - 根 CA
    - 中级

- 终结点证书
    - 节点证书
    - 本地 UI 证书
    - Azure 资源管理器证书
    - Blob 存储证书
    - IoT 设备证书
    <!--- WiFi certificates
    - VPN certificates-->

- 加密证书
    - 支持会话证书

以下部分详细介绍了这些证书。

## <a name="signing-chain-certificates"></a>签名链证书

这些是签署证书的机构或签署证书颁发机构的证书。 

### <a name="types"></a>类型

这些证书可以是根证书或中间证书。 根证书始终是自签名（或自行签名）。 中间证书不是自签名证书，由签署机构签名。

### <a name="caveats"></a>注意事项

- 根证书应为签名链证书。
- 可以按以下格式在设备上上传根证书： 
    - DER – 它们作为 `.cer` 文件扩展名提供。
    - Base-64 编码 – 它们作为 `.cer` 文件扩展名提供。
    - P7b – 此格式仅用于包含根证书和中间证书的签名链证书。
- 在上传任何其他证书之前，将始终上传签名链证书。


## <a name="node-certificates"></a>节点证书

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> 设备中的所有节点都将持续相互通信，因此需要建立信任关系。 节点证书提供了建立这种信任的方法。 当你通过 https 使用远程 PowerShell 会话连接到设备节点时，节点证书也会发挥作用。

### <a name="caveats"></a>注意事项

- 节点证书应以 `.pfx` 格式提供，并带有可以导出的私钥。 
- 你可以创建并上传 1 个通配符节点证书或 4 个单独的节点证书。 
- 如果 DNS 域发生更改，但设备名称未更改，则必须更改节点证书。 如果要自带节点证书，则无法更改设备序列号，只能更改域名。
- 使用下表指导你创建节点证书。
   
    |类型 |使用者名称 (SN)  |使用者可选名称 (SAN)  |使用者名称示例 |
    |---------|---------|---------|---------|
    |节点|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>终结点证书

对于设备公开的任何终结点，都需要证书才能进行可信通信。 终结点证书包括通过 REST API 访问 Azure 资源管理器和 blob 存储时所需的证书。 

如果自带签名证书，则还需要证书的相应签名链。 对于签名链、Azure 资源管理器和设备上的 blob 证书，你将需要在客户端计算机上使用相应的证书，以便对设备进行身份验证和与之通信。

### <a name="caveats"></a>注意事项

- 终结点证书的格式必须为 `.pfx`，并带有私钥。 签名链应为 DER 格式（`.cer` 文件扩展名）。 
- 自带终结点证书时，这些证书可以是单独的证书或多域证书。 
- 如果要引入签名链，则必须先上传签名链证书，然后才能上传终结点证书。
- 如果设备名称或 DNS 域名发生变更，则必须更改这些证书。
- 可以使用通配符终结点证书。
- 终结点证书的属性类似于典型的 SSL 证书属性。 
- 创建终结点证书时使用下表：

    |类型 |使用者名称 (SN)  |使用者可选名称 (SAN)  |使用者名称示例 |
    |---------|---------|---------|---------|
    |Azure 资源管理器|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob 存储|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |两个终结点的多个 SAN 单一证书|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>本地 UI 证书

可以通过浏览器访问设备的本地 Web UI。 若要确保此通信安全，可以上传自己的证书。 

### <a name="caveats"></a>注意事项

- 本地 UI 证书也以 `.pfx` 格式上传，包含可以导出的私钥。
- 上传本地 UI 证书后，你将需要重新启动浏览器并清除缓存。 请参阅浏览器的特定说明。

    |类型 |使用者名称 (SN)  |使用者可选名称 (SAN)  |使用者名称示例 |
    |---------|---------|---------|---------|
    |本地 UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge 设备证书

Azure Stack Edge Pro 设备也是一台 IoT 设备，其计算由连接到它的 IoT Edge 设备启用。 还可上传 IoT Edge 证书，以便在此 IoT Edge 设备和可能与其进行连接的下游设备之间进行任何安全通信。 

此设备具有自签名证书，如果你只想在这台设备上使用计算场景，则可以使用该证书。 但是，如果 Azure Stack Edge Pro 设备已连接到下游设备，则需要自带证书。

要启用此信任关系，需要安装三个 IoT Edge 证书：

- 根证书颁发机构或所有者证书颁发机构
- 设备证书颁发机构 
- 设备密钥证书

### <a name="caveats"></a>注意事项

- IoT Edge 证书以 `.pem` 格式上传。 


有关 IoT Edge 证书的更多信息，请查看 [Azure IoT Edge 证书详细信息](../iot-edge/iot-edge-certs.md#iot-edge-certificates)。

## <a name="support-session-certificates"></a>支持会话证书

如果 Azure Stack Edge Pro 设备遇到任何问题，则在解决这些问题时，可以在设备上打开远程 PowerShell 支持会话。 若要在此支持会话中启用安全加密通信，可以上传证书。

### <a name="caveats"></a>注意事项

- 请确保在使用解密工具的客户端计算机上安装了具有私钥的相应 `.pfx` 证书。
- 验证证书的“密钥用法”字段不是“证书签名”。 若要对此进行验证，请右键单击该证书，选择“打开”，然后在“详细信息”选项卡中查找“密钥用法”。 


### <a name="caveats"></a>注意事项

- 支持会话证书必须以 DER 格式提供，并带有 `.cer` 扩展名。


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>创建证书（可选）

以下部分介绍了创建签名链和终结点证书的过程。

### <a name="certificate-workflow"></a>证书工作流

你可以使用定义的方式为环境中运行的设备创建证书。 可以使用 IT 管理员提供的证书。 

对于仅开发或测试目的，还可以使用 Windows PowerShell 在本地系统上创建证书。 为客户端创建证书时，请遵循以下准则：

1. 你可以创建以下任何类型的证书：

    - 创建一个有效证书，与一个完全限定的域名 (FQDN) 一起使用。 例如，mydomain.com。
    - 还可以创建通配符证书来保护主域名和多个子域。 例如，.mydomain.com。
    - 创建使用者可选名称 (SAN) 证书，它将在一个证书中覆盖多个域名。 

2. 如果自带证书，则需要签名链的根证书。 有关步骤，请参阅[创建签名链证书](#create-signing-chain-certificate)。

3. 接下来，可以为设备、blob 和 Azure 资源管理器的本地 UI 创建终结点证书。 可以为设备、blob 和 Azure 资源管理器创建 3 个单独的证书，也可以为所有 3 个终结点创建一个证书。 有关详细步骤，请参阅[创建签名和终结点证书](#create-signed-endpoint-certificates)。

4. 无论是创建 3 个单独的证书还是一个证书，请根据为每个证书类型提供的指导，指定使用者名称 (SN) 和使用者可选名称 (SAN)。 

### <a name="create-signing-chain-certificate"></a>创建签名链证书

通过在管理员模式下运行的 Windows PowerShell 创建这些证书。 以这种方式创建的证书应只用于开发或测试目的。

签名链证书只需创建一次。 其他终结点证书将引用此证书进行签名。
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>创建签名的终结点证书

通过在管理员模式下运行的 Windows PowerShell 创建这些证书。

在这些示例中，使用以下项为设备创建终结点证书：
    - 设备名称：`DBE-HWDC1T2`
    - DNS 域：`microsoftdatabox.com`

替换为设备的名称和 DNS 域，以便为设备创建证书。
 
Blob 终结点证书

为个人存储中的 Blob 终结点创建证书。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Azure 资源管理器终结点证书

为个人存储中的 Azure 资源管理器终结点创建证书。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

设备本地 Web UI 证书

为个人存储中设备的本地 Web UI 创建证书。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

所有终结点的单个多 SAN 证书

为个人存储中的所有终结点创建单个证书。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

创建证书后，下一步是在 Azure Stack Edge Pro 设备上上传证书


## <a name="upload-certificates"></a>上传证书 

默认情况下，你为设备创建的证书驻留在客户端上的“个人存储”中。 这些证书需要在你的客户端上导出为合适的格式文件，然后才能上传到设备。

1. 根证书必须导出为包含 `.cer` 扩展名的 DER 格式。 有关详细步骤，请参阅将[将证书导出为 DER 格式](#export-certificates-as-der-format)。
2. 终结点证书必须导出为包含私钥的 .pfx 文件。 有关详细步骤，请参阅将[将证书导出为包含私钥的 .pfx 文件](#export-certificates-as-pfx-format-with-private-key)。 
3. 然后，在本地 Web UI 中使用“证书”页上的“+ 添加证书”选项，在设备上上传根证书和终结点证书。 

    1. 首先上传根证书。 在本地 Web UI 中，转到“证书”>“+ 添加证书”。

        ![添加签名链证书 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. 接下来上传终结点证书。 

        ![添加签名链证书 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        选择 .pfx 格式的证书文件，并输入在导出证书时提供的密码。 Azure 资源管理器证书可能需要几分钟才会应用。

        如果未首先更新签名链，并且尝试上传终结点证书，则会出现错误。

        ![应用证书错误](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        返回并上传签名链证书，然后上传和应用终结点证书。

> [!IMPORTANT]
> 如果更改了设备名称或 DNS 域，则必须创建新证书。 然后，应将客户端证书和设备证书更新为新的设备名称和 DNS 域。 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>在访问设备的客户端上导入证书

你创建并上传到设备上的证书必须导入到 Windows 客户端上（访问设备）相应的证书存储中。

1. 现在应在客户端系统上的“受信任的根证书颁发机构”中导入作为 DER 导出的根证书。 有关详细步骤，请参阅[将证书导入到受信任的根证书颁发机构存储](#import-certificates-as-der-format)。

2. 导出为 `.pfx` 的终结点证书必须导出为包含 `.cer` 扩展名的 DER。 然后，将 `.cer` 导入到系统上的“个人证书存储”中。 有关详细步骤，请参阅[将证书导入到个人证书存储](#import-certificates-as-der-format)。

### <a name="import-certificates-as-der-format"></a>以 DER 格式导入证书

若要在 Windows 客户端上导入证书，请执行以下步骤：

1. 右键单击文件并选择“安装证书”。 该操作会启动证书导入向导。

    ![导入证书 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. 对于“存储位置”，选择“本地计算机”，然后选择“下一步”。

    ![导入证书 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. 选择“将所有证书放入以下存储区”，然后选择“浏览”。 

    - 若要导入到个人存储，请导航到远程主机的“个人存储”，然后选择“下一步”。

        ![导入证书 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - 若要导入到受信任的存储区，请导航到“受信任的根证书颁发机构”，然后选择“下一步”。

        ![导入证书 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. 选择“完成”。 将显示一条提示已成功导入的消息。

### <a name="export-certificates-as-pfx-format-with-private-key"></a>将证书导出为包含私钥的 .pfx 格式

执行以下步骤，在 Windows 计算机上导出具有私钥的 SSL 证书。 

> [!IMPORTANT]
> 在用于创建证书的同一台计算机上执行以下步骤。 

1. 运行 certlm.msc 以启动本地计算机证书存储。

1. 双击“个人”文件夹，然后单击“证书”。

    ![导出证书 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. 右键单击要备份的证书，然后选择“所有任务”>“导出...”

    ![导出证书 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. 按照“证书导出向导”将证书备份到 .pfx 文件。

    ![导出证书 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. 选择“是，导出私钥”。

    ![导出证书 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. 选择“在证书路径中包括所有证书(如果可能)”、“导出所有扩展属性”和“启用证书隐私”。 

    > [!IMPORTANT]
    > 请不要选择“如果导出成功，请删除私钥选项”。

    ![导出证书 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. 请输入你能记住的密码。 确认该密码。 密码可保护私钥。

    ![导出证书 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. 选择在设置位置保存文件。

    ![导出证书 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. 选择“完成”。

    ![导出证书 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. 你会收到一条指示已成功导出的消息。 选择“确定”。

    ![导出证书 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

.pfx 文件备份现在保存到你选择的位置，并且已准备好进行移动或存储，以安全留存。


### <a name="export-certificates-as-der-format"></a>以 DER 格式导出证书

1. 运行 certlm.msc 以启动本地计算机证书存储。

1. 在“个人证书存储”中，选择根证书。 右键单击并选择“所有任务”->“导出...”

    ![导出证书 DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. 证书向导将打开。 选择格式“DER 编码的二进制 x.509 (.cer)”。 选择“**下一步**”。

    ![导出证书 DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. 浏览并选择要导出 .cer 格式文件的位置。

    ![导出证书 DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. 选择“完成”。

    ![导出证书 DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>支持的证书算法

 Azure Stack Edge Pro 设备仅支持 Rivest–Shamir–Adleman (RSA) 证书。 不支持椭圆曲线数字签名算法 (ECDSA) 证书。

 包含 RSA 公钥的证书称为 RSA 证书。 包含椭圆曲线加密 (ECC) 公钥的证书称为 ECDSA（椭圆曲线数字签名算法）证书。 


## <a name="view-certificate-expiry"></a>查看证书有效期

如果自带证书，证书将在 1 年或 6 个月后过期。 若要查看证书的到期日期，请转到设备本地 Web UI 的“证书”页。 如果选择特定证书，则可以查看证书的到期日期。

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates]().-->

## <a name="next-steps"></a>后续步骤

[部署 Azure Stack Edge Pro 设备](azure-stack-edge-gpu-deploy-prep.md)