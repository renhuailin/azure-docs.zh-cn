---
title: 如何为 Linux 配置 OpenSSL
titleSuffix: Azure Cognitive Services
description: 了解如何为 Linux 配置 OpenSSL。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577438"
---
# <a name="configure-openssl-for-linux"></a>为 Linux 配置 OpenSSL

使用 1.9.0 之前的任何语音 SDK 版本时，会将 [OpenSSL](https://www.openssl.org) 动态配置为主机系统版本。 在更高版本的语音 SDK 中，OpenSSL（版本为 [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)）会静态链接到语音 SDK 的核心库。

为了确保连接性，请验证是否已在系统中安装了 OpenSSL 证书。 运行一个命令：
```bash
openssl version -d
```

在基于 Ubuntu/Debian 的系统上，输出应该是：
```
OPENSSLDIR: "/usr/lib/ssl"
```

检查 OPENSSLDIR 下是否有 `certs` 子目录。 在上面的示例中，该目录会是 `/usr/lib/ssl/certs`。

* 如果有 `/usr/lib/ssl/certs` 并且其中包含多个单独的证书文件（带有 `.crt` 或 `.pem` 扩展名），则无需执行进一步的操作。

* 如果 OPENSSLDIR 是 `/usr/lib/ssl` 以外的其他内容，并且/或者存在单一的证书捆绑包文件而不是多个单独的文件，则需要设置相应的 SSL 环境变量来指示可以在哪里找到证书。

## <a name="examples"></a>示例

- OPENSSLDIR 为 `/opt/ssl`。 存在包含多个 `.crt` 或 `.pem` 文件的子目录 `certs`。
请在运行使用语音 SDK 的程序之前将环境变量 `SSL_CERT_DIR` 设置为指向 `/opt/ssl/certs`。 例如：
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR 为 `/etc/pki/tls`（例如，在基于 RHEL/CentOS 的系统上就是如此）。 有一个包含证书捆绑包文件（例如 `ca-bundle.crt`）的 `certs` 子目录。
请在运行使用语音 SDK 的程序之前将环境变量 `SSL_CERT_FILE` 设置为指向该文件。 例如：
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>证书吊销检查
在连接到语音服务时，语音 SDK 会验证语音服务使用的 TLS 证书是否尚未吊销。 为了执行此检查，语音 SDK 需要访问 Azure 使用的证书颁发机构的 CRL 分发点。 在[此文档](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes)中可以找到可以下载 CRL 的位置的列表。 如果证书已被吊销或无法下载 CRL，语音 SDK 会中止连接并引发“已取消”事件。

如果从中使用语音 SDK 的网络配置为不允许访问 CRL 下载位置，那么，在无法检索 CRL 的情况下，可以禁用 CRL 检查或将其设置为不会失败。 此配置是通过用于创建识别器对象的配置对象完成的。

若要在无法检索 CRL 时继续进行连接，请设置属性 OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE。

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
在设置为“true”时，将会尝试检索 CRL。如果检索成功，则会检查证书的吊销状态；如果检索失败，则会允许继续进行连接。

若要完全禁用证书吊销检查，请将属性 OPENSSL_DISABLE_CRL_CHECK 设置为“true”。
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> 另外需要注意，有些 Linux 发行版没有定义 TMP 或 TMPDIR 环境变量。 这会导致语音 SDK 每次都下载证书吊销列表 (CRL)，而不是将 CRL 缓存到磁盘重复使用，直至它们过期。 为了提高初始连接性能，可以[创建名为 TMPDIR 的环境变量并将其设置为所选临时目录的路径](https://help.ubuntu.com/community/EnvironmentVariables)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [关于语音 SDK](speech-sdk.md)
