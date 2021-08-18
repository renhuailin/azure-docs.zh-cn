---
title: 导出 Azure Cosmos DB 模拟器证书
description: 了解如何导出要用于 Java、Python 和 Node.js 应用的 Azure Cosmos DB 模拟器证书。 应当为未使用 Windows 证书存储的语言和运行时环境导出并使用证书。
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
ms.author: esarroyo
author: StefArroyo
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 669361fcc5ba5b79f0ec014b60fe4aa630b7b017
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113361416"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>导出要用于 Java、Python 和 Node.js 应用的 Azure Cosmos DB 模拟器证书
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

为方便进行开发，Azure Cosmos DB 模拟器提供了一个模拟 Azure Cosmos DB 服务的本地环境。 Azure Cosmos DB 模拟器仅支持通过 TLS 连接进行安全通信。

首次运行 Azure Cosmos DB 本地模拟器时，会在该模拟器中生成证书。 有两个证书。 其中一个用于连接到本地模拟器，另一个用于管理模拟器中模拟器数据的默认加密。 要导出的证书是友好名称为“DocumentDBEmulatorCertificate”的连接证书。

当你使用模拟器以各种语言（例如 Java、Python 或 Node.js）开发应用时，你需要导出模拟器证书，并将其导入到所需的证书存储中。

当应用程序在 Windows OS 主机上运行时，.NET 语言和运行时使用 Windows 证书存储来安全地连接到 Azure Cosmos DB 本地模拟器。 其他语言有自己管理和使用证书方法。 例如，Java 使用其自己的[证书存储](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)，Python 使用[套接字包装器](https://docs.python.org/2/library/ssl.html)，Node.js 使用 [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)。

本文演示了如何导出 TLS/SSL 证书，此类证书用于未集成 Windows 证书存储的各种语言和运行时环境。 可以在[使用 Azure Cosmos DB 模拟器进行开发和测试](./local-emulator.md)中了解有关模拟器的更多信息。

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>导出 Azure Cosmos DB TLS/SSL 证书

你需要导出模拟器证书，才能成功地通过未集成 Windows 证书存储的语言和运行时环境使用模拟器终结点。 你可以使用 Windows 证书管理器导出证书。 使用以下分步说明，将“DocumentDBEmulatorCertificate”证书导出为 BASE-64 编码的 X.509 (.cer) 文件：

1. 通过运行 certlm.msc 启动 Windows 证书管理器并导航到“个人”->“证书”文件夹，打开友好名称为“DocumentDbEmulatorCertificate”的证书。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 1":::

1. 单击“详细信息”，并单击“确定”。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 2":::

1. 单击“复制到文件...” 。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 3":::

1. 单击“下一步”。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 4":::

1. 单击“否，不导出私钥”，并单击“下一步”。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 5":::

1. 单击“Base-64 编码 X.509 (.CER)”，并单击“下一步”。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 6":::

1. 为证书指定名称。 在本示例中为“documentdbemulatorcert”。单击“下一步”。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 7":::

1. 单击“完成” 。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 8":::

## <a name="use-the-certificate-with-java-apps"></a>将证书用于 Java 应用

运行使用基于 Java 的客户端的 Java 应用程序或 MongoDB 应用程序时，将证书安装到 Java 默认证书存储比传递 `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` 标志更简单。 例如，包含的 Java 演示应用程序 (`https://localhost:8081/_explorer/index.html`) 依赖于默认证书存储。

请按照[将证书添加到 Java 证书存储](https://docs.oracle.com/cd/E54932_01/doc.705/e54936/cssg_create_ssl_cert.htm)中的说明将 X.509 证书导入到默认 Java 证书存储。 请注意，运行 keytool 时会在 %JAVA_HOME% 目录中执行操作。 证书导入证书存储后，SQL 和 Azure Cosmos DB API for MongoDB 的客户端就能连接到 Azure Cosmos DB 模拟器。

也可运行以下 bash 脚本来导入证书：

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

安装“CosmosDBEmulatorCertificate”TLS/SSL 证书后，应用程序应该能够连接并使用本地 Azure Cosmos DB 模拟器。 如果有任何问题，可以按照[调试 SSL/TLS 连接](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html)一文进行操作。 在大多数情况下，证书可能未安装到 %JAVA_HOME%/jre/lib/security/cacerts 存储中。 例如，如果已安装多个 Java 版本，则应用程序使用的 cacerts 存储可能与你更新的存储不同。

## <a name="use-the-certificate-with-python-apps"></a>将证书用于 Python 应用

从 Python 应用连接到模拟器时，会禁用 TLS 验证。 默认情况下，用于 SQL API 的 [Python SDK（2.0.0 或更高版本）](sql-api-sdk-python.md)在连接到本地模拟器时不会尝试使用 TLS/SSL 证书。 但是，如果要使用 TLS 验证，则可以按照 [Python 套接字包装器](https://docs.python.org/2/library/ssl.html)文档中的示例进行操作。

## <a name="how-to-use-the-certificate-in-nodejs"></a>如何在 Node.js 中使用证书

从 Node.js SDK 连接到模拟器时，会禁用 TLS 验证。 默认情况下，用于 SQL API 的 [Node.js SDK（1.10.1 或更高版本）](sql-api-sdk-node.md)在连接到本地模拟器时不会尝试使用 TLS/SSL 证书。 但是，如果要使用 TLS 验证，则可以按照 [Node.js 文档](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)中的示例进行操作。

## <a name="rotate-emulator-certificates"></a>轮换模拟器证书

可以通过在 Azure Cosmos DB 模拟器（在 Windows 任务栏中运行）中选择“重置数据”，强制重新生成模拟器证书。 请注意，此操作还会擦除模拟器在本地存储的所有数据。

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Azure Cosmos DB 本地模拟器重置数据":::

如果已将证书安装到 Java 证书存储中或在其他位置使用它们，则需要使用当前证书重新导入它们。 在更新证书之前，应用程序无法连接到本地模拟器。

## <a name="next-steps"></a>后续步骤

* [使用命令行参数和 PowerShell 命令控制模拟器](emulator-command-line-parameters.md)
* [使用模拟器调试问题](troubleshoot-local-emulator.md)
