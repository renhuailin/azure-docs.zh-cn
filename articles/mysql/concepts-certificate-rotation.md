---
title: Azure Database for MySQL 的证书轮换
description: 了解即将推出的会影响 Azure Database for MySQL 的根证书更改
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 9ad2566188256dd23b0f479c2576636750e33b02
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715099"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>了解 Azure Database for MySQL 单一服务器的根 CA 更改的更改

Azure Database for MySQL 单服务器已成功在 **2021 年2月15日 () 02/15/2021** 上完成根证书更改，作为标准维护和安全最佳方案的一部分。 本文将提供有关所做更改的详细信息、受影响的资源以及确保应用程序维持与数据库服务器的连接所需的步骤。

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。
>

## <a name="why-root-certificate-update-is-required"></a>为什么需要更新根证书？

Azure database for MySQL 用户只能使用预定义的证书来连接到 [此处](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)的 MySQL 服务器。 但是，[证书颁发机构 (CA) 浏览器论坛](https://cabforum.org/) 最近发布的报告表明，由 CA 供应商颁发的多个证书不符合标准。

根据行业的合规性要求，CA 供应商已开始吊销不符合标准的 CA 的 CA 证书，要求服务器使用由符合标准的 CA 颁发的证书，并由这些符合标准的 CA 颁发的 CA 证书进行签名。 由于 Azure Database for MySQL 使用这些不符合的证书之一，因此我们需要将证书轮换到兼容的版本，以最大程度地降低 MySQL 服务器的潜在威胁。

新证书已推出，从2021年2月15日起生效， (02/15/2021) 。 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>2021年2月15日 (02/15/2021) 执行了哪些更改？

2021年2月15日，已将 [baltimorecybertrustroot.crt.pem 根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)替换为同一 [baltimorecybertrustroot.crt.pem 根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)的 **兼容版本**，以确保现有客户无需更改任何内容，并且不会影响到服务器的连接。 在此更改过程中， [baltimorecybertrustroot.crt.pem 根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)**不会替换** 为 [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ，并且该更改将延迟，从而使客户有更多的时间进行更改。

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>是否需要在客户端上进行任何更改以维持连接？

不需要在客户端进行任何更改。 如果遵循了下面的建议，只要未从组合的 CA 证书中 **删除 baltimorecybertrustroot.crt.pem 证书** ，就仍然可以继续连接。 **建议不要从组合的 CA 证书中删除 Baltimorecybertrustroot.crt.pem，直到进一步通知维护连接。**

### <a name="previous-recommendation"></a>先前建议

若要避免应用程序因意外撤销证书而被中断，或要更新已吊销的证书，请使用以下步骤。 其思路是创建一个新的 *pem* 文件，该文件结合当前证书和新证书，在 SSL 证书验证期间，将使用其中一个允许值。 请参阅以下步骤：

* 从以下链接下载 Baltimorecybertrustroot.crt.pem & DigiCertGlobalRootG2 根 CA：

  * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

* 生成组合的 CA 证书存储，其中同时包含 **BaltimoreCyberTrustRoot** 和 **DigiCertGlobalRootG2** 证书。

  * 对于 Java (MySQL Connector/J) 用户，请执行以下命令：

    ```console
    keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
    ```

    然后，将原始密钥存储文件替换为新生成的密钥存储文件：

    * System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
    * System.setProperty("javax.net.ssl.trustStorePassword","password");

  * 对于 .NET (MySQL Connector/NET、MySQLConnector) users，请确保 **baltimorecybertrustroot.crt.pem** 和 **DigiCertGlobalRootG2** 都存在于 Windows 证书存储和受信任的根证书颁发机构中。 如果不存在任何证书，则导入缺少的证书。

    :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="Azure Database for MySQL .net 证书关系图":::

  * 对于使用 SSL_CERT_DIR 的 Linux 上的 .NET 用户，请确保 SSL_CERT_DIR 中的 **baltimorecybertrustroot.crt.pem** 和 **DigiCertGlobalRootG2** 都存在于所指示的目录中。 如果任何证书不存在，请创建缺少的证书文件。

  * 对于其他 (MySQL Client/MySQL 工作台/C/c + +/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) 用户，你可以将两个 CA 证书文件合并为以下格式：

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

* 将原始根 CA pem 文件替换为组合的根 CA 文件，然后重启应用程序/客户端。
* 将来，在服务器端部署新证书后，可以将 CA pem 文件更改为 DigiCertGlobalRootG2.crt.pem。

> [!NOTE]
> 在进行证书更改之前，请不要删除或更改 **巴尔的摩证书** 。 更改完成后，我们会发送讯息，之后用户便可以安全地删除 Baltimore 证书。 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>为什么在2021年2月15日此更改期间，Baltimorecybertrustroot.crt.pem 证书不会替换为 DigiCertGlobalRootG2？

我们评估了客户对此更改的准备情况，并认识到很多客户正在寻找其他提前时间来管理此更改。 在为客户提供更多的准备时间方面，我们决定将证书更改推迟到 DigiCertGlobalRootG2，至少一年为客户和最终用户提供足够的提前期。 

我们对用户的建议是，使用上述步骤创建合并的证书并连接到服务器，但不删除 Baltimorecybertrustroot.crt.pem 证书，直到我们发送通信来将其删除。 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>如果删除了 Baltimorecybertrustroot.crt.pem 证书，会怎么样？

连接到 Azure Database for MySQL 服务器时，会开始连接错误。 你将需要再次 [配置 SSL](howto-configure-ssl.md) 与 [baltimorecybertrustroot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 证书以维护连接。


## <a name="frequently-asked-questions"></a>常见问题

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. 如果我不使用 SSL/TLS，是否仍需要更新根 CA？

  如果不使用 SSL/TLS，则无需执行任何操作。

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. 如果我在使用 SSL/TLS，是否需要重新启动数据库服务器来更新根 CA？

不需要，你无需重新启动数据库服务器即可开始使用新证书。 此根证书是客户端更改，传入的客户端连接需要使用新证书来确保它们可以连接到数据库服务器。

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. 如何实现知道是否使用 SSL/TLS 进行根证书验证？

通过查看连接字符串，可以确定连接是否验证根证书。

- 如果你的连接字符串包含 `sslmode=verify-ca` 或 `sslmode=verify-identity`，则你需要更新证书。
- 如果连接字符串包括 `sslmode=disable` 、 `sslmode=allow` 、 `sslmode=prefer` 或 `sslmode=require` ，则无需更新证书。
- 如果连接字符串未指定 sslmode，则无需更新证书。

如果你使用的是将连接字符串抽象化的客户端，请查看客户端的文档，以了解其是否验证证书。

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mysql"></a>4.如果将应用服务与 Azure Database for MySQL 一起使用，会产生什么影响？

对于连接到 Azure Database for MySQL 的 Azure 应用服务，有两种可能的方案，具体取决于你在应用程序中使用 SSL 的方式。

* 此新证书已在平台级别添加到应用服务。 如果要在应用程序中使用应用服务平台上包含的 SSL 证书，则无需执行任何操作。 这是最常见的方案。 
* 如果你在代码中显式包含 SSL 证书文件的路径，则需要下载新证书，并生成如上所述的合并证书，并使用证书文件。 这种情况的一个很好的示例是，在应用服务中使用自定义容器，如 [应用服务文档](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)中所述。 这是一种不常见的方案，但我们已了解到一些用户使用此方案。

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>5.如果将 Azure Kubernetes 服务 (AKS) 与 Azure Database for MySQL 一起使用，会产生什么影响？

如果尝试使用 Azure Kubernetes Services (AKS) 连接到 Azure Database for MySQL，这类似于从专用客户主机环境进行访问。 请参考[此处](../aks/ingress-own-tls.md)的步骤。

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>6.使用 Azure 数据工厂连接到 Azure Database for MySQL 会产生什么影响？

对于使用 Azure Integration Runtime 的连接器，连接器在 Azure 托管的环境中利用 Windows 证书存储中的证书。 这些证书已与新应用的证书兼容，因此不需要执行任何操作。

对于使用自承载 Integration Runtime 的连接器，其中在连接字符串中显式包含 SSL 证书文件的路径，则需要下载 [新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ，并更新连接字符串以使用该证书。

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7.是否需要为此更改计划数据库服务器维护停机时间？

否。 由于此处的更改仅在客户端连接到数据库服务器，因此数据库服务器不需要任何维护停机时间即可进行此更改。

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. 如果我在2021年2月15日之后创建新的服务器 (02/15/2021) ，将会受到影响吗？

对于2021年2月15日之后创建的服务器 (02/15/2021) ，你将继续使用适用于你的应用程序的 [baltimorecybertrustroot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 来通过 SSL 连接。

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. Microsoft 更新其证书的频率或过期策略是什么？

Azure Database for MySQL 使用的这些证书是由受信任的证书颁发机构 (CA) 提供的。 因此，这些证书的支持与 CA 支持这些证书相关联。 [Baltimorecybertrustroot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)证书计划在2025后过期，因此 Microsoft 需要在过期前执行证书更改。 同样，如果这些预定义证书中存在未预见的 bug，Microsoft 将需要进行证书轮换，最早的情况是在2021年2月15日执行的更改，以确保服务始终安全且符合要求。

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10. 如果我使用的是读取副本，是否只需在源服务器或读取副本上执行此更新？

由于此更新是客户端更改，因此，如果客户端用于从副本服务器读取数据，则还需要对这些客户端应用更改。

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11. 如果我使用的是数据复制，是否需要执行任何操作？

如果使用 [数据复制](concepts-data-in-replication.md) 来连接到 Azure Database for MySQL，则需要考虑以下两个事项：

* 如果数据复制是从虚拟机（本地或 Azure 虚拟机）到 Azure Database for MySQL，则需要检查是否使用了 SSL 来创建副本。 运行 **SHOW SLAVE STATUS** 并检查以下设置。  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    如果看不到为 CA_file、SSL_Cert 和 SSL_Key 提供证书，则需要通过添加 [新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) 并创建合并的证书文件来更新文件。

* 如果数据复制在两个 Azure Database for MySQL 之间，则需要通过执行 **调用 mysql.az_replication_change_master** 来重置副本，并提供新的双重根证书作为最后一个参数 [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12.是否有服务器端查询可用来验证是否正在使用 SSL？

若要验证是否正在使用 SSL 连接连接到服务器，请参阅 [ssl 验证](howto-configure-ssl.md#step-4-verify-the-ssl-connection)。

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13.如果我的证书文件中已有 DigiCertGlobalRootG2，是否需要执行任何操作？

否。 如果你的证书文件已有 **DigiCertGlobalRootG2**，则无需执行任何操作。

### <a name="14-what-if-i-have-further-questions"></a>14.如果我有其他问题，该怎么办？

如果有疑问，请从 [Microsoft Q&的](mailto:AzureDatabaseforMySQL@service.microsoft.com)社区专家那里获取答案。 如果你有支持计划并需要技术帮助，请[联系我们](mailto:AzureDatabaseforMySQL@service.microsoft.com)。
