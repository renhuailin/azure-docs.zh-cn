---
title: Azure Database for MariaDB 的证书轮换
description: 了解即将推出的会影响 Azure Database for MariaDB 的根证书更改
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 8a8a7fec5b5c5f62fb44f6c0ccf6859b1141295b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646504"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>了解 Azure Database for MariaDB 的根 CA 更改中的更改

作为标准维护和安全最佳做法的一部分，Azure Database for MariaDB 已于 2021 年 2 月 15 日 (2021/02/15) 成功完成根证书更改。 本文提供了有关更改、受影响的资源的更多详细信息，以及确保应用程序始终与数据库服务器连接所需执行的步骤。

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。
>

## <a name="why-root-certificate-update-is-required"></a>为什么需要更新根证书？

Azure database for MariaDB 用户只能使用位于[此处](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)的预定义证书连接到 Azure Database for MariaDB 服务器。 但是，[证书颁发机构 (CA) 浏览器论坛](https://cabforum.org/) 最近发布的报告表明，由 CA 供应商颁发的多个证书不符合标准。

根据行业的合规性要求，CA 供应商已开始吊销不符合标准的 CA 的 CA 证书，要求服务器使用由符合标准的 CA 颁发的证书，并由这些符合标准的 CA 颁发的 CA 证书进行签名。 由于 Azure Database for MariaDB 使用其中一个不符合标准的证书，因此我们需要将证书轮换到合规版本，以最大程度减少对 MySQL 服务器的潜在威胁。

新证书已推出，自 2021 年 2 月 15 日 (2021/02/15) 起生效。 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>2021 年 2 月 15 日 (2021/02/15) 进行了哪些更改？

2021 年 2 月 15 日，已将 [BaltimoreCyberTrustRoot 根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)替换为同一 [BaltimoreCyberTrustRoot 根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)的合规版本，以确保现有客户无需更改任何内容，并且不会影响他们与服务器的连接。 在此更改过程中，[BaltimoreCyberTrustRoot 根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)未替换为 [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)，并且推迟了该更改，以使客户有更多时间进行更改。

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>我是否需要在客户端上进行任何更改以维护连接？

客户端不需要进行任何更改。 如果遵循以下之前的建议，只要未从组合的 CA 证书中删除 BaltimoreCyberTrustRoot 证书，就仍然可以继续连接。 在收到有关维护连接的进一步通知之前，建议不要从组合的 CA 证书中删除 BaltimoreCyberTrustRoot。

### <a name="previous-recommendation"></a>先前的建议

- 从以下链接下载 BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 CA ：

  - [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  - [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

- 生成组合的 CA 证书存储，其中同时包含 **BaltimoreCyberTrustRoot** 和 **DigiCertGlobalRootG2** 证书。

  - 对于 Java (MariaDB Connector/J) 用户，请执行以下命令：

    ```console
    keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
    ```

    然后，将原始密钥存储文件替换为新生成的密钥存储文件：

    - `System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");`
    - `System.setProperty("javax.net.ssl.trustStorePassword","password");`

  - 对于 .NET（MariaDB Connector/NET、MariaDBConnector）用户，请确保 BaltimoreCyberTrustRoot 和 DigiCertGlobalRootG2 都存在于 Windows 证书存储“受信任的根证书颁发机构”中 。 如果有任何证书不存在，请导入缺失的证书。

    [![Azure Database for MariaDB .net 证书](media/overview/netconnecter-cert.png)](media/overview/netconnecter-cert.png#lightbox)

  - 对于 Linux 上使用 SSL_CERT_DIR 的 .NET 用户，请确保 BaltimoreCyberTrustRoot 和 DigiCertGlobalRootG2 都存在于 SSL_CERT_DIR 指示的目录中 。 如果有任何证书不存在，请创建缺失的证书文件。

  - 对于其他（MariaDB 客户端/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift）用户，你可以按以下格式合并两个 CA 证书文件

   ```
   -----BEGIN CERTIFICATE-----
   (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   (Root CA2: DigiCertGlobalRootG2.crt.pem)
   -----END CERTIFICATE-----
   ```

- 将原始根 CA pem 文件替换为组合的根 CA 文件，然后重启应用程序/客户端。
- 将来，在服务器端部署新证书后，可以将 CA pem 文件更改为 DigiCertGlobalRootG2.crt.pem。

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>为什么在 2021 年 2 月 15 日进行此更改期间，未将 BaltimoreCyberTrustRoot 证书替换为 DigiCertGlobalRootG2？

我们评估了客户对此更改的准备情况，意识到很多客户都需要额外的准备时间来完成此更改。 为了给客户提供更多的准备时间，我们决定把“证书更改为 DigiCertGlobalRootG2”的时限推迟至少一年，以便为客户和最终用户提供充足的准备时间。 

我们对用户的建议是，使用上述步骤创建组合的证书并连接到服务器，且在我们发送“将 BaltimoreCyberTrustRoot 证书删除”的讯息之前，不要将其删除。 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>如果删除了 BaltimoreCyberTrustRoot 证书会怎么样？

在连接到 Azure Database for MariaDB 服务器时，会开始出现连接错误。 你将需要再次使用 [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 证书[配置 SSL](howto-configure-ssl.md) 以正常连接。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1.如果我不使用 SSL/TLS，是否仍需要更新根 CA？

如果你不使用 SSL/TLS，则无需执行任何操作。

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2.如果我使用 SSL/TLS，是否需要重启数据库服务器来更新根 CA？

不需要，你无需重启数据库服务器即可开始使用新证书。 证书更新是客户端更改，传入的客户端连接需要使用新证书来确保它们可以连接到数据库服务器。

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3.如何知道是否使用 SSL/TLS 进行根证书验证？

通过查看连接字符串，可以确定连接是否验证根证书。

- 如果你的连接字符串包含 `sslmode=verify-ca` 或 `sslmode=verify-identity`，则你需要更新证书。
- 如果你的连接字符串包含 `sslmode=disable`、`sslmode=allow`、`sslmode=prefer` 或 `sslmode=require`，则不需要更新证书。
- 如果你的连接字符串未指定 sslmode，则不需要更新证书。

如果你使用的客户端会将连接字符串提取出来，请查看客户端的文档，了解它是否会验证证书。

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4.如果将应用服务与 Azure Database for MariaDB 一起使用，会产生什么影响？

对于连接到 Azure Database for MariaDB 的 Azure 应用服务，可以采用两种可能的方案，具体取决于你在应用程序中使用 SSL 的方式。

- 此新证书已在平台级别添加到应用服务。 如果你在应用程序中使用应用服务平台上包含的 SSL 证书，则无需执行任何操作。 这是最常见的情况。 
- 如果在代码中显式包含 SSL 证书文件的路径，则需要下载新证书并更新代码以使用新证书。这种情况的一个很好的示例是在应用服务中使用[应用服务文档](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)中共享的自定义容器。 这是不常见的情况，但我们已了解到一些用户正在使用它。

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5.如果将 Azure Kubernetes 服务 (AKS) 与 Azure Database for MariaDB 一起使用，会产生什么影响？

如果尝试使用 Azure Kubernetes 服务 (AKS) 连接到 Azure Database for MariaDB，这与从专用客户主机环境访问类似。 请参考[此处](../aks/ingress-own-tls.md)的步骤。

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6.使用 Azure 数据工厂连接到 Azure Database for MariaDB 会产生什么影响？

对于使用 Azure Integration Runtime 的连接器，连接器在 Azure 托管的环境中利用 Windows 证书存储中的证书。 这些证书已与新应用的证书兼容，因此不需要执行任何操作。

对于使用自承载集成运行时的连接器，你在连接字符串中显式包含 SSL 证书文件的路径，因此需要下载[新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)并更新连接字符串以使用该证书。

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7.是否需要为此更改计划数据库服务器维护停机时间？

否。 由于此处的更改仅发生在要连接到数据库服务器的客户端，因此数据库服务器不需要维护停机时间来进行此更改。

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8.如果我在 2021 年 2 月 15 日 (2021/02/15) 之后创建新的服务器，我是否会受影响？

对于在 2021 年 2 月 15 日 (2021/02/15) 之后创建的服务器，将继续为应用程序使用 [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)，通过 SSL 进行连接。

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9.Microsoft 更新其证书的频率是多少？或者说过期策略是怎样的？

Azure Database for MariaDB 使用的这些证书是由受信任的证书颁发机构 (CA) 提供的。 因此，这些证书的支持与 CA 对这些证书的支持相关联。 [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 证书计划于 2025 年到期，因此 Microsoft 需要在到期前执行证书更改。 此外，如果这些预定义证书中存在未预见的 bug，Microsoft 将需要尽早执行证书轮换（类似于在 2021 年 2 月 15 日执行的更改），以确保服务始终安全且合规。

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10.如果我使用的是只读副本，是否只需在源服务器或只读副本上执行此更新？

由于此更新是客户端更改，因此，如果客户端过去从副本服务器读取数据，则还需要对这些客户端应用更改。

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11.如果我使用的是数据传入复制，是否需要执行任何操作？

- 如果数据复制是从虚拟机（本地或 Azure 虚拟机）到 Azure Database for MySQL，则需要检查是否使用了 SSL 来创建副本。 运行 **SHOW SLAVE STATUS** 并检查以下设置。

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

如果你使用[数据传入复制](concepts-data-in-replication.md)来连接到 Azure Database for MySQL，则需要考虑以下两个事项：

- 如果数据复制是从虚拟机（本地或 Azure 虚拟机）到 Azure Database for MySQL，则需要检查是否使用了 SSL 来创建副本。 运行 **SHOW SLAVE STATUS** 并检查以下设置。 

  ```azurecli-interactive
  Master_SSL_Allowed            : Yes
  Master_SSL_CA_File            : ~\azure_mysqlservice.pem
  Master_SSL_CA_Path            :
  Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
  Master_SSL_Cipher             :
  Master_SSL_Key                : ~\azure_mysqlclient_key.pem
  ```

  如果你看到为 CA_file、SSL_Cert 和 SSL_Key 提供了证书，则需要通过添加[新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)来更新文件，并创建合并的证书文件。

- 如果数据复制发生在两个 Azure Database for MySQL 之间，则需要通过执行 CALL mysql.az_replication_change_master 来重置副本，并提供新的双重根证书作为最后一个参数 [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication)。

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12.是否有服务器端查询可用来验证是否正在使用 SSL？

若要验证是否正在使用 SSL 连接来连接到服务器，请参阅 [SSL 验证](howto-configure-ssl.md#verify-the-ssl-connection)。

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13.如果我的证书文件中已有 DigiCertGlobalRootG2，是否需要执行任何操作？

否。 如果你的证书文件已经有 DigiCertGlobalRootG2，则无需执行任何操作。

### <a name="14-what-if-i-have-further-questions"></a>14.如果我有其他问题，该怎么办？

如果有疑问，请从 [Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com) 中的社区专家那里获取解答。 如果你有支持计划并需要技术帮助，请[联系我们](mailto:AzureDatabaseformariadb@service.microsoft.com)。
