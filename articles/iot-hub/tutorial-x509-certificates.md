---
title: 教程 - 了解 Azure IoT 中心的 X.509 公钥证书 | Microsoft Docs
description: 教程 - 了解 Azure IoT 中心的 X.509 公钥证书
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
ms.openlocfilehash: f0dc843460c1d48ae74e6d417b771d478dd097b8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724377"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>教程：了解 X.509 公钥证书

X.509 证书是代表用户、计算机、服务或设备的数字文档。 这些证书由证书颁发机构 (CA)、从属 CA 或注册机构颁发，并包含证书使用者的公钥。 X.509 证书并不包含使用者的私钥（必须安全存储）。 公钥证书由 [RFC 5280](https://tools.ietf.org/html/rfc5280) 记录。 它们已进行数字签名，并且通常包含以下信息：

* 证书使用者的信息
* 与使用者私钥相对应的公钥
* 证书颁发机构的信息
* 支持的加密和/或数字签名算法
* 用于确定证书吊销和有效性状态的信息

## <a name="certificate-fields"></a>证书字段

久而久之，现已有三个证书版本。 每个版本都会在前一个版本的基础上添加字段。 版本 3 是最新版本，除包含自身字段外，还包含版本 1 和版本 2 字段。 版本 1 定义以下字段：

* 版本：标识证书版本号的值（1、2 或 3）
* 序列号：由 CA 颁发的每份证书的唯一编号
* CA 签名算法：CA 用于签署证书内容的算法名称
* 颁发者名称：证书颁发机构的可分辨名称 (DN)
* 有效期：证书有效的时间段
* 使用者名称：证书所代表的实体名称
* 使用者公钥信息：证书使用者所拥有的公钥

版本 2 添加了以下字段，其中包含证书颁发者的信息。 不过，这些字段很少使用。

* 颁发者唯一 ID：由 CA 定义的证书颁发机构的唯一标识符
* 使用者唯一 ID：由证书颁发机构定义的证书使用者的唯一标识符

版本 3 证书添加了以下扩展：

* 颁发机构密钥标识符：可以是以下两个值其中之一：
  * 由 CA 颁发的证书的使用者和序列号
  * 颁发此证书的 CA 的公钥哈希
* 使用者密钥标识符：当前证书的公钥哈希
* 密钥用法：定义可为其使用证书的服务。 该选项可以是如下一个或多个值：
  * 数字签名
  * 不可否认性
  * **密钥加密**
  * 数据加密
  * 密钥协议
  * 密钥证书签名
  * CRL 签名
  * 仅加密
  * 仅解密
* 私钥使用期限：密钥对的私钥部分的有效期
* 证书策略：用于验证证书使用者的策略
* 策略映射：将一个组织的策略映射到另一个组织的策略中
* 使用者可选名称：使用者的替代名称列表
* 颁发者可选名称：证书颁发机构的替代名称列表
* 使用者 Dir 属性：X 500 或 LDAP 目录的属性
* 基本约束：允许证书指定是将其颁发给 CA，还是颁发给用户、计算机、设备或服务。 此扩展还包括一个路径长度约束，用于限制可以存在的从属 CA 的数量。
* 名称约束：指定在 CA 颁发的证书中允许使用的命名空间
* 策略约束：可用于禁止 CA 之间的策略映射
* 扩展密钥用法：表示在“密钥用法”扩展中标识的目的之外如何使用证书的公钥
* CRL 分发点：包含一个或多个可在其上发布基本证书吊销列表 (CRL) 的 URL
* 禁止任何策略：禁止在从属 CA 证书中使用“所有颁发策略” OID (2.5.29.32.0)
* 最新 CRL：包含一个或多个可在其上发布证书颁发机构增量 CRL 的 URL
* 颁发机构信息访问：包含一个或多个可在其上发布 CA 证书的 URL
* 使用者信息访问：包含有关如何检索证书使用者的其他详情的信息

## <a name="certificate-formats"></a>证书格式

可以采用多种格式保存证书。 Azure IoT 中心身份验证通常使用 PEM 和 PFX 格式。

### <a name="binary-certificate"></a>二进制证书

该证书包含使用 DER ASN. 1 编码的原始形式二进制证书。

### <a name="ascii-pem-format"></a>ASCII PEM 格式

PEM 证书（扩展名为 .pem）包含以 -----BEGIN CERTIFICATE----- 开头并以 -----END CERTIFICATE----- 结尾的 base64 编码证书。 PEM 格式非常常见，在上传某些证书时 IoT 中心需要此格式。

### <a name="ascii-pem-key"></a>ASCII (PEM) 密钥

包含 base64 编码的 DER 密钥，该密钥可能包含有关用于密码保护的算法的其他元数据。

### <a name="pkcs7-certificate"></a>PKCS#7 证书

一种用于传输已签名或已加密数据的格式。 该证书由 [RFC 2315](https://tools.ietf.org/html/rfc2315) 定义。 它可以包括整个证书链。

### <a name="pkcs8-key"></a>PKCS#8 密钥

一种私钥存储格式，由 [RFC 5208](https://tools.ietf.org/html/rfc5208) 定义。

### <a name="pkcs12-key-and-certificate"></a>PKCS#12 密钥和证书

一种复杂的格式，可以存储和保护密钥和整个证书链。 它通常与 .pfx 扩展名一起使用。 PKCS#12 等同于 PFX 格式。

## <a name="for-more-information"></a>更多信息

有关详细信息，请参阅以下主题：

* [适用于非专业人士的 X.509 证书术语指南](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [概念性理解 IoT 行业中的 X.509 CA 证书](./iot-hub-x509ca-concept.md)

## <a name="next-steps"></a>后续步骤

若要生成可用于对 IoT 中心的设备进行身份验证的测试证书，请参阅以下主题：

* [使用 Microsoft 提供的脚本创建测试证书](tutorial-x509-scripts.md)
* [使用 OpenSSL 创建测试证书](tutorial-x509-openssl.md)
* [使用 OpenSSL 创建自签名测试证书](tutorial-x509-self-sign.md)

如果你有证书颁发机构 (CA) 证书或从属 CA 证书，并且想要将其上传到 IoT 中心并证明自己拥有该证书，请参阅[证明对 CA 证书的所有权](tutorial-x509-prove-possession.md)。