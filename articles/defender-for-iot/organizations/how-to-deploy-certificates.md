---
title: 部署证书
description: 了解如何设置并部署用于 Defender for IoT 的证书。
ms.date: 08/29/2021
ms.topic: how-to
ms.openlocfilehash: 83345d9ad0267e39093bc310c8ebd940f878c47e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439825"
---
# <a name="about-certificates"></a>关于证书

本文提供了创建和部署用于 Defender for IoT 的证书时所需的信息。 安全主管、PKI 或其他合格证书主管应处理证书的创建和部署。

Defender for IoT 使用 SSL/TLS 证书来保护以下系统组件之间的通信： 

- 用户与设备的 Web 控制台之间。 
- 传感器与本地管理控制台之间。 
- 管理控制台和高可用性管理控制台之间。
- 传感器和本地管理控制台上的 REST API 之间。 

Defender for IoT 管理用户可以从 SSL/TLS 证书对话框中将证书上传到传感器控制台及其本地管理控制台。

:::image type="content" source="media/how-to-deploy-certificates/certificate-upload.png" alt-text="证书-上传对话框。":::

## <a name="about-certificate-generation-methods"></a>关于证书生成的方法

支持使用以下各种证书生成方法：  

- 专用和企业密钥基础结构（专用 PKI） 
- 公钥基础结构（公共 PKI） 
- 在设备上本地生成的证书（本地自签名）。 

> [!Important]
> 不建议使用本地自签名证书。 此类型的连接不安全，应仅用于测试环境。 由于无法验证证书的所有者，也无法维护系统的安全，因此切勿将自签名证书用于生产网络。

## <a name="about-certificate-validation"></a>关于证书验证

除了保护系统组件之间的通信安全，用户还可执行证书验证。  

针对以下内容评估验证：

- 证书吊销列表 (CRL)
- 证书到期日期  
- 证书信任链

执行两次验证：

1. 将证书上传到传感器和本地管理控制台时。 如果验证失败，则无法上传证书。
1. 在以下内容之间启动加密通信时：

    - Defender for IoT 系统组件，例如，传感器和本地管理控制台。

    - Defender for IoT 和在转发规则中定义的某些第三方服务器。  有关详细信息，可参阅[关于转发的警报信息](how-to-forward-alert-information-to-partners.md#about-forwarded-alert-information)。  

如果验证失败，则相关组件之间的通信将会停止，并在控制台中显示验证错误。

## <a name="about-certificate-upload-to-defender-for-iot"></a>关于将证书上传到 Defender for IoT

安装传感器和本地管理控制台后，将生成一个本地自签名证书，该证书用于访问传感器和本地管理控制台 Web 应用程序。

首次登录到传感器和本地管理控制台时，系统会提示管理员用户上传 SSL/TLS 证书。 强烈建议使用 SSL/TLS 证书。

如果证书主管未正确创建证书或证书存在连接问题，则无法上传证书，用户将被迫使用本地签名证书。  

用于验证已上传证书和第三方证书的选项会自动启用，但可以禁用。 如果禁用，则即使证书无效，组件之间的加密通信也仍将继续。

## <a name="certificate-deployment-tasks"></a>证书部署任务

本部分介绍确保证书部署顺利运行所需执行的步骤。

**若要部署证书，请确保：**

- 安全专家、PKI 或证书专家正在创建或监视证书创建。 
- 为每个传感器、管理控制台和 HA 计算机创建唯一证书。
- 满足证书创建要求。 请参阅 [证书创建要求](#certificate-creation-requirements)。
- 登录到每个 Defender for IoT 传感器、本地管理控制台和 HA 计算机的管理员用户都有权访问该证书。

## <a name="certificate-creation-requirements"></a>证书创建要求

本部分介绍证书创建要求，包括：

- [证书验证的端口访问要求](#port-access-requirements-for-certificate-validation)

- [文件类型要求](#file-type-requirements)

- [密钥文件要求](#key-file-requirements)

- [证书链文件要求（如果使用 .pem）](#certificate-chain-file-requirements-if-pem-is-used)

### <a name="port-access-requirements-for-certificate-validation"></a>证书验证的端口访问要求

如果使用证书验证，请验证对端口 80 的访问是否可用。

根据证书吊销列表和证书到期日期对证书验证进行评估。 这意味着，设备应能够与证书定义的 CRL 服务器建立连接。 默认情况下，证书将引用 HTTP 端口 80 上的 CRL URL。 

某些组织安全策略可能会阻止对此端口的访问。 如果组织无法访问端口 80，可以： 

1. 定义证书中的另一个 URL 和特定端口。

    - URL 应定义为 http://，而不是 https://。

    - 验证目标 CRL 服务器能否侦听定义的端口。 

1. 使用将访问端口 80 上的 CRL 的代理服务器。

### <a name="file-type-requirements"></a>文件类型要求

Defender for IoT 要求每个 CA 签名证书都包含一个 .key 文件和一个 .crt 文件。 登录后，会将这些文件上传到传感器和本地管理控制台。 某些组织可能需要 .pem 文件。 Defender for IoT 不需要此文件类型。

**.crt  - 证书容器文件**  
具有不同扩展名的 .pem 或 .der 格式的文件。 Windows 资源管理器将该文件识别为证书。 Windows 资源管理器无法识别 .pem 文件。

**.key - 私钥文件**  
密钥文件的格式与 PEM 文件的格式相同，但它具有不同的扩展名。

**.pem - 证书容器文件（可选）** PEM 是一个文本文件，其中包含证书文本的 Base64 编码，纯文本标头和用于标记证书的开头和结尾的页脚。

可能需要将现有文件类型转换为支持的类型。 有关详细信息，请参阅[将现有文件转换为支持的文件](#convert-existing-files-to-supported-files)。

### <a name="certificate-file-parameter-requirements"></a>证书文件参数要求

创建证书之前，请验证是否满足以下参数要求：

- [CRT 文件要求](#crt-file-requirements)
- [密钥文件要求](#key-file-requirements)
- [证书链文件要求（如果使用 .pem）](#certificate-chain-file-requirements-if-pem-is-used)

### <a name="crt-file-requirements"></a>CRT 文件要求

本部分介绍 .crt 字段要求。

- 签名算法 = SHA256RSA 
- 签名哈希算法 = SHA256 
- 有效起始日期 = 有效的过去日期 
- 有效结束日期 = 有效的将来日期 
- 公钥 = RSA 2048 位（最小）或 4096 位 
- CRL 分发点 = 指向 .crl 文件的 URL 
- 使用者中国区（公用名）= 设备的域名；例如，Sensor.contoso.com 或 *.contoso.com。  
- 使用者国家/地区 (C) = 定义的值，例如 US 
- 使用者组织单位 (OU) = 定义的值，例如 Contoso Labs 
- 使用者组织 (O) = 定义的值，例如 Contoso Inc. 

包含其他参数的证书可能有效，但 Microsoft 不支持它们。  

### <a name="key-file-requirements"></a>密钥文件要求

使用 RSA 2048 位或 4096 位。

如果使用的密钥长度为 4096 位，则每个连接开始时的 SSL 握手速度会变慢。 此外，握手期间 CPU 使用率会增加。

### <a name="certificate-chain-file-requirements-if-pem-is-used"></a>证书链文件要求（如果使用 .pem）

一个 .pem 文件，其中包含导向证书的信任链中所有证书颁发机构的证书。  

证书链文件中支持 Bag 属性。

## <a name="create-certificates"></a>创建证书

使用证书管理平台创建证书，例如自动化 PKI 管理平台。 验证证书是否满足证书文件要求。 有关测试所创建文件的信息，请参阅测试证书。

如果未执行证书验证，请删除证书中的 CRL URL 引用。 有关此参数的信息，请参阅 [CRT 文件要求](#crt-file-requirements)。

如果没有可自动创建证书的应用程序，请咨询安全主管、PKI 或其他合格证书主管。

可以[测试创建的证书](#test-certificates-you-create)。  

如果不想创建新证书文件，也可以转换现有证书文件。 有关详细信息，请参阅[将现有文件转换为支持的文件](#convert-existing-files-to-supported-files)。

### <a name="sample-certificate"></a>示例证书

可以将证书与下面的示例证书进行比较。 验证相同的字段是否存在，以及字段的顺序是否相同。

:::image type="content" source="media/how-to-deploy-certificates/sample-certificate.png" alt-text="示例-证书。":::

## <a name="test-certificates-you-create"></a>测试创建的证书

在将证书部署到传感器和本地管理控制台之前，可以先测试证书。 如果要检查证书 .csr 文件或私钥文件中的信息，请使用以下命令：

| **测试** | **CLI 命令** |
|--|--|
| 检查证书签名请求 (CSR) | openssl req -text -noout -verify -in CSR.csr |
| 检查私钥  | openssl rsa -in privateKey.key -check  |
| 检查证书  | openssl x509 -in certificate.crt -text -noout |

如果这些测试失败，请查看[证书文件参数要求](#certificate-file-parameter-requirements)以验证文件参数是否准确，或咨询证书主管。

## <a name="convert-existing-files-to-supported-files"></a>将现有文件转换为支持的文件 

本部分介绍如何将现有证书文件转换为支持的格式。

|**说明** | **CLI 命令** |
|--|--|
| 将 .crt 文件转换为 .pem 文件   | openssl x509 -inform PEM -in <full path>/<pem-file-name>.pem -out <fullpath>/<crt-file-name>.crt  | 
| 将 .pem 文件转换为 .crt 文件   | openssl x509 -inform PEM -in <full path>/<pem-file-name>.pem -out <fullpath>/<crt-file-name>.crt |  
| 将包含私钥和证书的 PKCS#12 文件 (.pfx .p12) 转换为 .pem   | openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes. 可以添加 -nocerts 以仅输出私钥，或添加 -nokeys 以仅输出证书。  |  

## <a name="troubleshooting"></a>疑难解答  

本部分介绍在证书上传和验证期间可能会发生的各种问题，以及解决这些问题的步骤。

### <a name="troubleshoot-ca-certificate-upload"></a>排查 CA 证书上传问题  

如果未正确创建证书或证书无效，则尝试首次登录到传感器或本地管理控制台的管理员用户将无法上传 CA 签名的证书。 如果证书上传失败，将显示一条或多条错误消息：

| **证书验证错误** | 建议 |
|--|--|
| 密码与密钥不匹配 | 验证是否键入了正确的密码。 如果问题持续出现，请尝试使用正确的密码重新创建证书。 |
| 无法验证信任链。 提供的证书和根 CA 不匹配。  | 确保 .pem 文件与 .crt 文件关联。 如果问题持续出现，请尝试使用正确的信任链（由 .pem 文件定义）重新创建证书。 |
| 此 SSL 证书已过期，被视为无效。  | 创建具有有效日期的新证书。|
| 此 SSL 证书已过期，被视为无效。  | 创建具有有效日期的新证书。|
|此证书已被 CRL 吊销，因此在安全连接中不可信 | 创建新的未吊销证书。 |
|无法访问 CRL（证书吊销列表）位置。 验证是否可以从此设备访问 URL | 请确保网络配置允许设备访问证书中定义的 CRL 服务器。如果建立直接连接存在限制，可以使用代理服务器。  
|证书验证失败  | 这表示设备中出现常规错误。 请联系 [Microsoft 支持部门](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099)。|

### <a name="troubleshoot-file-conversions"></a>排查文件转换问题  

文件转换可能无法创建有效的证书。 例如，文件结构可能不准确。

如果转换失败：  

- 使用[将现有文件转换为支持的文件](#convert-existing-files-to-supported-files)中所述的转换命令。
- 确保文件参数准确无误。 有关详细信息，请参阅[文件类型要求](#file-type-requirements)和[证书文件参数要求](#certificate-file-parameter-requirements)。  
- 请咨询证书主管。