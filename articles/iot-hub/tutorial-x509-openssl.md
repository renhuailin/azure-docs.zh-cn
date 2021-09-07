---
title: 教程 - 使用 OpenSSL 为 Azure IoT 中心创建 X.509 测试证书 | Microsoft Docs
description: 教程 - 使用 OpenSSL 为 Azure IoT 中心创建 CA 和设备证书
author: v-gpettibone
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: adbb2979fc9e097fa0abf2675759ba1f7aad8a0c
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310571"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>教程：使用 OpenSSL 创建测试证书

尽管可以从受信任的证书颁发机构购买 X.509 证书，但创建自己的测试证书层次结构或使用自签名证书足以测试 IoT 中心设备身份验证。 以下示例使用 [OpenSSL](https://www.openssl.org/) 和 [OpenSSL 指南](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html)创建证书颁发机构 (CA)、从属 CA 和设备证书。 然后，该示例将从属 CA 和设备证书签名到证书层次结构中。 此处仅作示例之用。

## <a name="step-1---create-the-root-ca-directory-structure"></a>步骤 1 - 创建根 CA 目录结构

为证书颁发机构创建目录结构。

* certs 目录用于存储新证书。
* db 目录用于证书数据库。
* 专用目录用于存储 CA 私钥。

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>步骤 2 - 创建根 CA 配置文件

创建 CA 之前，请创建一个配置文件，并将其在 rootca.目录中另存为 `rootca.conf`。

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>步骤 3 - 创建根 CA

首先，在 rootca 目录中生成密钥和证书签名请求 (CSR)。

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

接下来，创建自签名 CA 证书。 自签名适用于测试目的。 在命令行上指定 ca_ext 配置文件扩展名。 这些扩展名表示证书适用于根 CA，可用于签署证书和证书吊销列表 (CRL)。 签署证书，并将其提交至数据库。

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>步骤 4 - 创建从属 CA 目录结构

为从属 CA 创建目录结构。

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>步骤 5 - 创建从属 CA 配置文件

创建配置文件并将其在目录 `subca` 中另存为 subca。

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days             = 365
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>步骤 6 - 创建从属 CA

在 `rootca/db/serial` 文件中为从属 CA 证书创建新序列号。

```bash
  openssl rand -hex 16 > ../rootca/db/serial
```

>[!IMPORTANT]
>必须为所创建的每个从属 CA 证书和每个设备证书创建新序列号。 不同的证书不能具有相同的序列号。

此示例演示如何创建从属 CA 或注册 CA。 由于可以使用根 CA 签署证书，因此不一定要创建从属 CA。 但是，拥有从属 CA 确实可以模拟真实的证书层次结构，在该层次结构中，根 CA 处于脱机状态，从属 CA 为客户端颁发证书。

使用配置文件生成密钥和证书签名请求 (CSR)。

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

将 CSR 提交到根 CA，使用根 CA 颁发并签署从属 CA 证书。 在命令行上为扩展开关指定 sub_ca_ext。 这些扩展表明，此证书适用于可签署证书和证书吊销列表 (CRL) 的 CA。 出现提示时，请签署证书，并将其提交到数据库。

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>步骤 7 - 展示所有权证明

现在，你同时拥有根 CA 证书和从属 CA 证书。 你可以使用其中任何一个来签署设备证书。 必须将你选择的证书上传到 IoT 中心。 假使你使用的是从属 CA 证书，可采取如下步骤。 若要将从属 CA 证书上传至 IoT 中心并进行注册，请执行以下操作：

1. 在 Azure 门户中，导航到 IoT 中心，选择“设置”>“证书”。

2. 选择“添加”，以添加新的从属 CA 证书。

3. 在“证书名称”字段中输入显示名称，并选择之前创建的 PEM 证书文件。

> [!NOTE]
> 上面创建的 .crt 证书与 .pem 证书相同。 只需在为证明所有权而上传证书时更改扩展，或使用以下 OpenSSL 命令：

```bash
openssl x509 -in mycert.crt -out mycert.pem -outform PEM
```

4. 选择“保存”。 此时证书显示在证书列表中，状态为“未验证”。 此验证过程将证明你已拥有证书。

   
5. 选择证书以查看“证书详细信息”对话框。

6. 选择“生成验证码”。 有关详细信息，请参阅[证明对 CA 证书的所有权](tutorial-x509-prove-possession.md)。

7. 将验证码复制到剪贴板。 必须将验证码设置为证书使用者。 例如，如果验证码为 BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A，请将其添加为证书使用者，如步骤 9 所示。

8. 生成私钥。

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. 从私钥中生成证书签名请求 (CSR)。 添加验证码作为证书使用者。

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

10. 使用根 CA 配置文件和所有权证明证书的 CSR 创建证书。

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. 在“证书详细信息”视图中选择新证书。 若要查找 PEM 文件，请导航到“certs”文件夹。

12. 上传证书后，选择“验证”。 CA 证书状态应更改为“已验证”。

## <a name="step-8---create-a-device-in-your-iot-hub"></a>步骤 8 - 在 IoT 中心创建设备

在 Azure 门户中，导航到 IoT 中心，并使用以下值创建新 IoT 设备标识：

1. 提供与设备证书的使用者名称匹配的设备 ID。

1. 选择“X.509 CA 签名”身份验证类型。

1. 选择“保存”。

## <a name="step-9---create-a-client-device-certificate"></a>步骤 9 - 创建客户端设备证书

若要生成客户端证书，必须首先生成私钥。 以下命令演示如何使用 OpenSSL 创建私钥。 在 subca 目录中创建密钥。

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

为密钥创建证书签名请求 (CSR)。 无需输入质询密码或可选公司名称。 但是，必须在“公用名”字段中输入设备 ID。

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

检查 CSR 是否符合预期。

```bash
openssl req -text -in device.csr -noout
```

将 CSR 发送到从属 CA，以登录到证书层次结构。 在 `-extensions` 开关中指定 `client_ext`。 请注意，已颁发证书中的 `Basic Constraints` 表示此证书不适用于 CA。 若要签署多个证书，请确保在使用 openssl `rand -hex 16 > db/serial` 命令生成每个证书之前更新序列号。

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>后续步骤

前往[“测试证书身份验证”](tutorial-x509-test-certificate.md)，以确定证书是否可在 IoT 中心对设备进行身份验证。
