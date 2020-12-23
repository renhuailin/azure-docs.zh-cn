---
title: Azure Database for MariaDB 的证书轮换
description: 了解即将推出的会影响 Azure Database for MariaDB 的根证书更改
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 02c0ecfc24b65afd46d75464b5411cfd5cf61857
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591525"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>了解 Azure Database for MariaDB 的根 CA 更改中的更改

Azure Database for MariaDB 将更改通过 SSL 启用的客户端应用程序/驱动程序的根证书，该证书用于[连接到数据库服务器](concepts-connectivity-architecture.md)。 根据标准维护和安全最佳做法的要求，当前可用的根证书已设置为在 2021 年 2 月 15 日 (2021/02/15) 到期。 本文更详细地介绍了即将推出的更改、会受影响的资源，以及确保应用程序始终与数据库服务器连接所需执行的步骤。

>[!NOTE]
> 根据客户的反馈，我们已扩展了2020年2月15日到到2021的现有巴尔的摩根 CA 的根证书弃用。 如果用户受到影响，我们希望此次延长能为他们提供足够的提前期来实施客户端更改。

## <a name="what-update-is-going-to-happen"></a>打算进行什么样的更新？

在某些情况下，应用程序使用基于受信任的证书颁发机构 (CA) 证书文件生成的本地证书文件才能安全地进行连接。 目前，客户只能使用位于[此处](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)的预定义证书连接到 Azure Database for MariaDB 服务器。 但是，[证书颁发机构 (CA) 浏览器论坛](https://cabforum.org/) 最近发布的报告表明，由 CA 供应商颁发的多个证书不符合标准。

根据行业的合规性要求，CA 供应商已开始吊销不符合标准的 CA 的 CA 证书，要求服务器使用由符合标准的 CA 颁发的证书，并由这些符合标准的 CA 颁发的 CA 证书进行签名。 由于 Azure Database for MariaDB 当前使用其中一个不符合标准的证书，客户端应用程序使用这些证书来验证其 SSL 连接，因此，我们需要确保采取适当的措施（如下所述），以最大程度地减少对 MariaDB 服务器的潜在影响。


新证书将从 2021 年 2 月 15 日 (2021/02/15) 开始使用。如果你在从 MySQL 客户端连接时使用服务器证书的 CA 验证或完整验证（sslmode=verify-ca 或 sslmode=verify-full），则需要在 2021 年 2 月 15 日 (2021/02/15) 之前更新应用程序配置。

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>如何知道我的数据库是否会受影响？

使用 SSL/TLS 并验证根证书的所有应用程序都需要更新根证书。 通过查看连接字符串，可以确定连接是否验证根证书。
-    如果你的连接字符串包含 `sslmode=verify-ca` 或 `sslmode=verify-identity`，则你需要更新证书。
-    如果你的连接字符串包含 `sslmode=disable`、`sslmode=allow`、`sslmode=prefer` 或 `sslmode=require`，则你不需要更新证书。 
-    如果你的连接字符串未指定 sslmode，则你不需要更新证书。

如果你使用的客户端将连接字符串提取出来，请查看客户端的文档，以了解它是否验证证书。
若要了解 Azure Database for MariaDB sslmode，请查看 [SSL 模式说明](concepts-ssl-connection-security.md#default-settings)。

若要避免应用程序的可用性因证书被意外吊销而中断，或要更新已吊销的证书，请参阅 [ **“我需要做什么来维护连接”**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)部分。

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>我需要做什么来维护连接

若要避免应用程序的可用性因证书被意外吊销而中断，或要更新已吊销的证书，请执行以下步骤。 其思路是创建一个新的 .pem 文件，该文件组合了当前证书和新证书，在 SSL 证书验证期间将使用允许的值之一。 请参考以下步骤：

*   从以下链接下载 **baltimorecybertrustroot.crt.pem**  &  **DigiCertGlobalRootG2** CA：
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   生成组合的 CA 证书存储，其中同时包含 **BaltimoreCyberTrustRoot** 和 **DigiCertGlobalRootG2** 证书。
    *   对于 Java (MariaDB Connector/J) 用户，请执行以下命令：

          ```console
          keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          然后，将原始密钥存储文件替换为新生成的密钥存储文件：
        *   System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file"); 
        *   System.setProperty("javax.net.ssl.trustStorePassword","password");
    *   对于 .NET (MariaDB Connector/NET，MariaDBConnector) 用户，请确保 **baltimorecybertrustroot.crt.pem** 和 **DigiCertGlobalRootG2** 都存在于 Windows 证书存储和受信任的根证书颁发机构中。 如果有任何证书不存在，请导入缺失的证书。

        ![Azure Database for MariaDB .net 证书](media/overview/netconnecter-cert.png)

    *   对于使用 SSL_CERT_DIR 的 Linux 上的 .NET 用户，请确保 SSL_CERT_DIR 中的 **baltimorecybertrustroot.crt.pem** 和 **DigiCertGlobalRootG2** 都存在于所指示的目录中。 如果有任何证书不存在，请创建缺失的证书文件。

    *   对于其他（MariaDB 客户端/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift）用户，你可以按以下格式合并两个 CA 证书文件</b>

        </br>-----BEGIN CERTIFICATE-----
 </br> (根 CA1： Baltimorecybertrustroot.crt.pem) 
 </br>-----END CERTIFICATE-----
 </br>-----BEGIN CERTIFICATE-----
 </br>(Root CA2:DigiCertGlobalRootG2.crt.pem)
 </br>-----END CERTIFICATE-----

*   将原始根 CA pem 文件替换为组合的根 CA 文件，然后重启应用程序/客户端。
*    将来，在服务器端部署新证书后，可以将 CA pem 文件更改为 DigiCertGlobalRootG2.crt.pem。

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>不更新证书会产生什么影响？
如果使用此处所述的 Azure Database for MariaDB 颁发的证书，则可能会中断应用程序的可用性，因为数据库将无法访问。 根据你的应用程序，你可能会收到各种错误消息，其中包括但不限于：
*    证书/已吊销的证书无效
*    连接超时

> [!NOTE]
> 在进行证书更改之前，请不要删除或更改 **巴尔的摩证书** 。 更改完成后，我们会发送讯息，之后用户便可以安全地删除 Baltimore 证书。 

## <a name="frequently-asked-questions"></a>常见问题

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1.如果我不使用 SSL/TLS，是否仍需要更新根 CA？
如果不使用 SSL/TLS，则无需执行任何操作。 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2.如果我使用 SSL/TLS，是否需要重启数据库服务器来更新根 CA？
不需要，你无需重启数据库服务器即可开始使用新证书。 证书更新是客户端更改，传入的客户端连接需要使用新证书来确保它们可以连接到数据库服务器。

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-february-15-2021-02152021"></a>3.如果在 2021 年 2 月 15 日 (2021/02/15) 之前未更新根证书，会发生什么情况？
如果在 2021 年 2 月 15 日之前未更新根证书，则通过 SSL/TLS 进行连接并对根证书进行验证的应用程序将无法与 MariaDB 数据库服务器通信，应用程序会遇到 MariaDB 数据库服务器的连接问题。

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4.如果将应用服务与 Azure Database for MariaDB 一起使用，会产生什么影响？
对于连接到 Azure Database for MariaDB 的 Azure 应用服务，可以采用两种可能的方案，具体取决于你在应用程序中使用 SSL 的方式。
*   此新证书已在平台级别添加到应用服务。 如果你在应用程序中使用应用服务平台上包含的 SSL 证书，则无需执行任何操作。
*   如果你在代码中显式包含 SSL 证书文件的路径，则需要下载新证书，并更新代码以使用新证书。此方案的一个很好的示例是，在应用服务中使用自定义容器，如[应用服务文档](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)中所述

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5.如果将 Azure Kubernetes 服务 (AKS) 与 Azure Database for MariaDB 一起使用，会产生什么影响？
如果尝试使用 Azure Kubernetes 服务 (AKS) 连接到 Azure Database for MariaDB，这与从专用客户主机环境访问类似。 请参考[此处](../aks/ingress-own-tls.md)的步骤。

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6.使用 Azure 数据工厂连接到 Azure Database for MariaDB 会产生什么影响？
对于使用 Azure Integration Runtime 的连接器，连接器在 Azure 托管的环境中利用 Windows 证书存储中的证书。 这些证书已与新应用的证书兼容，因此不需要执行任何操作。

对于使用自承载 Integration Runtime 的连接器，你在连接字符串中显式包含 SSL 证书文件的路径，因此需要下载[新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)并更新连接字符串以使用该证书。

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7.是否需要为此更改计划数据库服务器维护停机时间？
否。 由于此处的更改仅发生在要连接到数据库服务器的客户端，因此数据库服务器不需要维护停机时间来进行此更改。

### <a name="8--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-february-15-2021-02152021"></a>8.如果在 2021 年 2 月 15 日 (2021/02/15) 之前无法安排停机时间来进行此更改，该怎么办？
由于用于连接到服务器的客户端需要如[此处](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)的修复部分所述更新证书信息，因此在这种情况下，服务器不需要停机。

### <a name="9-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>9.如果我在 2021 年 2 月 15 日 (2021/02/15) 之后创建新的服务器，我是否会受影响？
对于在 2021 年 2 月 15 日 (2021/02/15) 之后创建的服务器，可以使用应用程序的新颁发的证书通过 SSL 进行连接。

###    <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10.Microsoft 更新其证书的频率是多少？或者说过期策略是怎样的？
Azure Database for MariaDB 使用的这些证书是由受信任的证书颁发机构 (CA) 提供的。 因此，Azure Database for MariaDB 对这些证书的支持与 CA 对这些证书的支持相关联。 但是，这些预定义证书中可能存在无法预料的 bug，这些 bug 需要尽早进行修复，正如此示例一样。

###    <a name="11-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>11.如果我使用的是只读副本，是否只需在源服务器或只读副本上执行此更新？
由于此更新是客户端更改，因此，如果客户端过去从副本服务器读取数据，则还需要对这些客户端应用更改。

###    <a name="12-if-i-am-using-data-in-replication-do-i-need-to-perform-any-action"></a>12.如果我使用的是数据传入复制，是否需要执行任何操作？

如果你使用[数据传入复制](concepts-data-in-replication.md)来连接到 Azure Database for MySQL，则需要考虑以下两个事项：

> [!NOTE]
>  本文包含对字词从属的引用，这是 Microsoft 不再使用的术语。 从软件中删除该字词后，我们会将其从本文中删除。
*   如果数据复制是从虚拟机（本地或 Azure 虚拟机）到 Azure Database for MySQL，则需要检查是否使用了 SSL 来创建副本。 运行 **SHOW SLAVE STATUS** 并检查以下设置。 

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    如果你看到为 CA_file、SSL_Cert 和 SSL_Key 提供了证书，则需要通过添加[新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)来更新文件。

*   如果数据复制发生在两个 Azure Database for MySQL 之间，则需要通过执行 **CALL mysql.az_replication_change_master** 来重置副本，并提供新的双重根证书作为最后一个参数 [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication)。

### <a name="13-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>13.是否有服务器端查询可用来验证是否正在使用 SSL？
若要验证是否正在使用 SSL 连接来连接到服务器，请参阅 [SSL 验证](howto-configure-ssl.md#verify-the-ssl-connection)。

### <a name="14-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>14.如果我的证书文件中已有 DigiCertGlobalRootG2，是否需要执行任何操作？
否。 如果你的证书文件已经有 **DigiCertGlobalRootG2**，则无需执行任何操作。

###    <a name="15-what-if-i-have-further-questions"></a>15.如果我有其他问题，该怎么办？
如果有疑问，请从 [Microsoft Q&的](mailto:AzureDatabaseformariadb@service.microsoft.com)社区专家那里获取答案。 如果你有支持计划并需要技术帮助，请[联系我们](mailto:AzureDatabaseformariadb@service.microsoft.com)。
