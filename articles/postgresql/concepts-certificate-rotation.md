---
title: Azure Database for PostgreSQL 单一服务器的证书轮换
description: 了解即将推出的会影响 Azure Database for PostgreSQL 单一服务器的根证书更改
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 6dc687879eb646b4abd081b40bce292d20ff3186
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123983"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>了解 Azure Database for PostgreSQL 单一服务器的根 CA 更改中的更改

Azure Database for PostgreSQL 单服务器已成功在 **2021 年2月15日 () 02/15/2021** 上完成根证书更改，作为标准维护和安全最佳方案的一部分。 本文将提供有关所做更改的详细信息、受影响的资源以及确保应用程序维持与数据库服务器的连接所需的步骤。

## <a name="why-root-certificate-update-is-required"></a>为什么需要更新根证书？

Azure database for PostgreSQL 用户只能使用预定义的证书来连接到 [此处](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)的 PostgreSQL 服务器。 但是，[证书颁发机构 (CA) 浏览器论坛](https://cabforum.org/) 最近发布的报告表明，由 CA 供应商颁发的多个证书不符合标准。

根据行业的合规性要求，CA 供应商已开始吊销不符合标准的 CA 的 CA 证书，要求服务器使用由符合标准的 CA 颁发的证书，并由这些符合标准的 CA 颁发的 CA 证书进行签名。 由于 Azure Database for MySQL 使用这些不符合的证书之一，因此我们需要将证书轮换到兼容的版本，以最大程度地降低 MySQL 服务器的潜在威胁。

新证书已推出，从2021年2月15日起生效， (02/15/2021) 。 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>2021年2月15日 (02/15/2021) 执行了哪些更改？

2021年2月15日，已将 [baltimorecybertrustroot.crt.pem 根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)替换为同一 [baltimorecybertrustroot.crt.pem 根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)的 **兼容版本**，以确保现有客户无需更改任何内容，并且不会影响到服务器的连接。 在此更改过程中， [baltimorecybertrustroot.crt.pem 根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)**不会替换** 为 [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ，并且该更改将延迟，从而使客户有更多的时间进行更改。

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>是否需要在客户端上进行任何更改以维持连接？

不需要在客户端进行任何更改。 如果遵循了下面的建议，只要未从组合的 CA 证书中 **删除 baltimorecybertrustroot.crt.pem 证书** ，就仍然可以继续连接。 **建议不要从组合的 CA 证书中删除 Baltimorecybertrustroot.crt.pem，直到进一步通知维护连接。**

### <a name="previous-recommendation"></a>先前建议

*   从以下链接下载 Baltimorecybertrustroot.crt.pem & DigiCertGlobalRootG2 根 CA：
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   生成组合的 CA 证书存储，其中同时包含 **BaltimoreCyberTrustRoot** 和 **DigiCertGlobalRootG2** 证书。
    *   对于使用 DefaultJavaSSLFactory 的 Java (PostgreSQL JDBC) 用户，请执行：

          ```console
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          然后，将原始密钥存储文件替换为新生成的密钥存储文件：
        *   System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file"); 
        *   System.setProperty("javax.net.ssl.trustStorePassword","password");
        
    *   对于 Windows 上的 .NET (Npgsql) 用户，请确保 **巴尔的摩 CyberTrust root** 和 **DigiCert Global root G2** 都存在于 Windows 证书存储和受信任的根证书颁发机构中。 如果有任何证书不存在，请导入缺失的证书。

        ![Azure Database for PostgreSQL .net 证书](media/overview/netconnecter-cert.png)

    *   对于使用 SSL_CERT_DIR 的 Linux 上的 .NET (Npgsql) 用户，请确保 **baltimorecybertrustroot.crt.pem** 和 **DigiCertGlobalRootG2** 都存在于 SSL_CERT_DIR 指示的目录中。 如果有任何证书不存在，请创建缺失的证书文件。

    *   对于其他 PostgreSQL 客户端用户，你可以按以下格式合并两个 CA 证书文件

        </br>-----BEGIN CERTIFICATE-----
 </br> (根 CA1： Baltimorecybertrustroot.crt.pem) 
 </br>-----END CERTIFICATE-----
 </br>-----BEGIN CERTIFICATE-----
 </br>(Root CA2:DigiCertGlobalRootG2.crt.pem)
 </br>-----END CERTIFICATE-----

*   将原始根 CA pem 文件替换为组合的根 CA 文件，然后重启应用程序/客户端。
*    将来，在服务器端部署新证书后，可以将 CA pem 文件更改为 DigiCertGlobalRootG2.crt.pem。

> [!NOTE]
> 在进行证书更改之前，请不要删除或更改 **巴尔的摩证书** 。 更改完成后，我们会发送讯息，之后用户便可以安全地删除 Baltimore 证书。 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>为什么在2021年2月15日此更改期间，Baltimorecybertrustroot.crt.pem 证书不会替换为 DigiCertGlobalRootG2？

我们评估了客户对此更改的准备情况，并认识到很多客户正在寻找其他提前时间来管理此更改。 在为客户提供更多的准备时间方面，我们决定将证书更改推迟到 DigiCertGlobalRootG2，至少一年为客户和最终用户提供足够的提前期。 

我们对用户的建议是，使用上述步骤创建合并的证书并连接到服务器，但不删除 Baltimorecybertrustroot.crt.pem 证书，直到我们发送通信来将其删除。 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>如果删除了 Baltimorecybertrustroot.crt.pem 证书，会怎么样？

连接到 Azure Database for PostgreSQL 服务器时，会开始连接错误。 你将需要再次配置 SSL 与 [baltimorecybertrustroot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 证书以维护连接。


## <a name="frequently-asked-questions"></a>常见问题

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1.如果我不使用 SSL/TLS，是否仍需要更新根 CA？
如果不使用 SSL/TLS，则无需执行任何操作。 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2.如果我使用 SSL/TLS，是否需要重启数据库服务器来更新根 CA？
不需要，你无需重启数据库服务器即可开始使用新证书。 这是客户端更改，传入的客户端连接需要使用新证书来确保它们可以连接到数据库服务器。

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. 如何实现知道是否使用 SSL/TLS 进行根证书验证？

通过查看连接字符串，可以确定连接是否验证根证书。
-    如果你的连接字符串包含 `sslmode=verify-ca` 或 `sslmode=verify-full`，则你需要更新证书。
-    如果你的连接字符串包含 `sslmode=disable`、`sslmode=allow`、`sslmode=prefer` 或 `sslmode=require`，则你不需要更新证书。 
-    如果你的连接字符串未指定 sslmode，则你不需要更新证书。

如果你使用的客户端将连接字符串提取出来，请查看客户端的文档，以了解它是否验证证书。 若要了解 PostgreSQL sslmode，请参阅 PostgreSQL 文档中的 [SSL 模式说明](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions)。

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4.如果将应用服务与 Azure Database for PostgreSQL 一起使用，会产生什么影响？
对于连接到 Azure Database for PostgreSQL 的 Azure 应用服务，可以采用两种可能的方案，具体取决于你在应用程序中使用 SSL 的方式。
*   此新证书已在平台级别添加到应用服务。 如果你在应用程序中使用应用服务平台上包含的 SSL 证书，则无需执行任何操作。
*   如果你在代码中显式包含 SSL 证书文件的路径，则需要下载新证书，并更新代码以使用新证书。此方案的一个很好的示例是，在应用服务中使用自定义容器，如[应用服务文档](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)中所述

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5.如果将 Azure Kubernetes 服务 (AKS) 与 Azure Database for PostgreSQL 一起使用，会产生什么影响？
如果尝试使用 Azure Kubernetes 服务 (AKS) 连接到 Azure Database for PostgreSQL，这与从专用客户主机环境访问类似。 请参考[此处](../aks/ingress-own-tls.md)的步骤。

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6.使用 Azure 数据工厂连接到 Azure Database for PostgreSQL 会产生什么影响？
对于使用 Azure Integration Runtime 的连接器，连接器在 Azure 托管的环境中利用 Windows 证书存储中的证书。 这些证书已与新应用的证书兼容，因此不需要执行任何操作。

对于使用自承载 Integration Runtime 的连接器，你在连接字符串中显式包含 SSL 证书文件的路径，因此需要下载[新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)并更新连接字符串以使用该证书。

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7.是否需要为此更改计划数据库服务器维护停机时间？
否。 由于此处的更改仅发生在要连接到数据库服务器的客户端，因此数据库服务器不需要维护停机时间来进行此更改。

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. 如果我在2021年2月15日之后创建新的服务器 (02/15/2021) ，将会受到影响吗？
对于2021年2月15日之后创建的服务器 (02/15/2021) ，你将继续使用适用于你的应用程序的 [baltimorecybertrustroot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 来通过 SSL 连接。

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. Microsoft 更新其证书的频率或过期策略是什么？
Azure Database for PostgreSQL 使用的这些证书是由受信任的证书颁发机构 (CA) 提供的。 因此，这些证书的支持与 CA 支持这些证书相关联。 [Baltimorecybertrustroot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)证书计划在2025后过期，因此 Microsoft 需要在过期前执行证书更改。 同样，如果这些预定义证书中存在未预见的 bug，Microsoft 将需要进行证书轮换，最早的情况是在2021年2月15日执行的更改，以确保服务始终安全且符合要求。

### <a name="10-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-the-primary-server-or-the-read-replicas"></a>10. 如果我使用的是读取副本，是否只需在主服务器或读取副本上执行此更新？
由于此更新是客户端更改，因此，如果客户端过去从副本服务器读取数据，则还需要对这些客户端应用更改。 

### <a name="11-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>11. 我们是否有服务器端查询来验证是否正在使用 SSL？
若要验证是否正在使用 SSL 连接来连接到服务器，请参阅 [SSL 验证](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity)。

### <a name="12-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>12. 如果我的证书文件中已有 DigiCertGlobalRootG2，是否需要执行操作？
否。 如果你的证书文件已经有 **DigiCertGlobalRootG2**，则无需执行任何操作。

### <a name="13-what-if-you-are-using-docker-image-of-pgbouncer-sidecar-provided-by-microsoft"></a>13. 如果使用 Microsoft 提供的 PgBouncer 挎斗的 docker 映像，会怎么样？
支持 [**巴尔的摩**](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)和 [**DigiCert**](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)的新 Docker 映像 (最新标记) 发布 [到下面。](https://hub.docker.com/_/microsoft-azure-oss-db-tools-pgbouncer-sidecar) 可以提取此新映像，以避免从2021年2月15日起连接中断。 

### <a name="14-what-if-i-have-further-questions"></a>14.如果我有其他问题，该怎么办？
如果有疑问，请从 [Microsoft Q&的](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)社区专家那里获取答案。 如果你有支持计划并需要技术  [帮助，请联系我们](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)
