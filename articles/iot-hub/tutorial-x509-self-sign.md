---
title: 教程 - 使用 OpenSSL 为 Azure IoT 中心创建自签名证书 | Microsoft Docs
description: 教程 - 使用 OpenSSL 为 Azure IoT 中心创建自签名的 X.509 证书
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
ms.openlocfilehash: 7d6bd2233c4167a992d3493fb7bee9d5b5466586
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740381"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>教程：使用 OpenSSL 创建自签名证书

可以使用两个自签名设备证书在 IoT 中心对设备进行身份验证。 此功能有时称作指纹身份验证，因为证书包含提交到 IoT 中心的指纹（哈希值）。 以下步骤说明如何创建两个自签名证书。

## <a name="step-1---create-a-key-for-the-first-certificate"></a>步骤 1 - 创建第一个证书的密钥

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>步骤 2 - 创建第一个证书的 CSR

出现提示时，请确保指定设备 ID。

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>步骤 3 - 检查 CSR

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>步骤 4 - 对证书 1 进行自签名

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>步骤 5 - 创建证书 2 的密钥

出现提示时，请指定用于证书 1 的同一设备 ID。

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>步骤 6 - 对证书 2 进行自签名

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>步骤 7 - 检索证书 1 的指纹

```bash
openssl x509 -in device.crt -noout -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>步骤 8 - 检索证书 2 的指纹

```bash
openssl x509 -in device2.crt -noout -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>步骤 9 - 创建新的 IoT 设备

在 Azure 门户中导航到你的 IoT 中心，并创建具有以下特征的新 IoT 设备标识：

* 提供与两个证书的使用者名称匹配的 **设备 ID**。
* 选择“X.509 自签名”身份验证类型。
* 粘贴从设备主要证书和辅助证书中复制的十六进制字符串指纹。 确保十六进制字符串不包含冒号分隔符。

## <a name="next-steps"></a>后续步骤

前往[“测试证书身份验证”](tutorial-x509-test-certificate.md)，以确定证书是否可在 IoT 中心对设备进行身份验证。
