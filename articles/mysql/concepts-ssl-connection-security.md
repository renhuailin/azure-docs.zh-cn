---
title: SSL/TLS 连接 - Azure Database for MySQL
description: 有关配置 Azure Database for MySQL 和关联应用程序以正确使用 SSL 连接的信息
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: be2023f47906aeef41884f21f67f1adb926b0b72
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653446"
---
# <a name="ssltls-connectivity-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的 SSL/TLS 连接

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL 支持使用安全套接字层 (SSL) 将数据库服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。

> [!NOTE]
> 更新 `require_secure_transport` 服务器参数值不会影响 MySQL 服务的行为。 使用本文中概述的 SSL 和 TLS 增强功能来保护与数据库的连接。

>[!NOTE]
> 根据客户的反馈，我们已将现有 Baltimore 根 CA 颁发的根证书的弃用时间延长至 2021 年 2 月 15 日 (2021/02/15)。

> [!IMPORTANT] 
> SSL 根证书设置为 2021 年 2 月 15 日 (2021/02/15) 到期。 请更新应用程序以使用[新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)。 若要了解详细信息，请参阅[计划内证书更新](concepts-certificate-rotation.md)

## <a name="ssl-default-settings"></a>SSL 默认设置

默认情况下，应将数据库服务配置为需要 SSL 连接才可连接到 MySQL。  建议尽量不要禁用 SSL 选项。

通过 Azure 门户和 CLI 预配新的 Azure Database for MySQL 服务器时，默认情况下会强制实施 SSL 连接。 

Azure 门户中显示了各种编程语言的连接字符串。 这些连接字符串包含连接到数据库所需的 SSL 参数。 在 Azure 门户中，选择服务器。 在“设置”标题下，选择“连接字符串” 。 SSL 参数因连接器而异，例如“ssl=true”、“sslmode=require”或“sslmode=required”，以及其他变体。

在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件生成的本地证书文件才能实现安全连接。 目前，客户只能使用预定义的证书来连接位于 Azure 中国https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem的 Azure Database For MySQL 服务器。 

同样，这些链接指向主权云中的服务器证书：[Azure 政府](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)、[Azure 中国](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)和 [Azure 德国](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt)。

若要了解如何在开发应用程序期间启用或禁用 SSL 连接，请参阅[如何配置 SSL](howto-configure-ssl.md)。

## <a name="tls-enforcement-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的 TLS 强制

对于使用传输层安全性 (TLS) 连接到数据库服务器的客户端，Azure Database for MySQL 支持加密。 TLS 是一种行业标准协议，可确保在数据库服务器与客户端应用程序之间实现安全的网络连接，使你能够满足合规性要求。

### <a name="tls-settings"></a>TLS 设置

Azure Database for MySQL 提供了为客户端连接强制使用 TLS 版本的功能。 若要强制使用 TLS 版本，请使用“最低 TLS 版本”选项设置。 此选项设置允许以下值：

|  最低 TLS 设置             | 支持的客户端 TLS 版本                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled（默认值） | 不需要 TLS                      |
| TLS1_0                           | TLS 1.0、TLS 1.1、TLS 1.2 及更高版本           |
| TLS1_1                           | TLS 1.1、TLS 1.2 及更高版本                   |
| TLS1_2                           | TLS 版本 1.2 及更高版本                     |


例如，将此最低 TLS 设置版本的值设置为 TLS 1.0 意味着服务器将允许那些使用 TLS 1.0、1.1 和 1.2+ 的客户端进行连接。 也可将此选项设置为 1.2，这意味着仅允许那些使用 TLS 1.2+ 的客户端进行连接，将拒绝使用 TLS 1.0 和 TLS 1.1 进行的所有连接。

> [!Note] 
> 默认情况下，Azure Database for MySQL 不强制执行最低 TLS 版本要求（设置为 `TLSEnforcementDisabled`）。
>
> 一旦强制实施最低 TLS 版本要求后，以后将无法禁用最低版本强制实施。

服务器处于联机状态时，不需要重启服务器，即可设置最低 TLS 版本设置。 若要了解如何为 Azure Database for MySQL 设置 TLS 设置，请参阅 [如何配置 TLS 设置](howto-tls-configurations.md)。

## <a name="cipher-support-by-azure-database-for-mysql-single-server"></a>Azure Database for MySQL 单一服务器提供的密码支持

根据 SSL/TLS 通信的要求，密码套件会被验证，只有受支持的密码套件才能与数据库服务器通信。 密码套件验证在[网关层](concepts-connectivity-architecture.md#connectivity-architecture)中控制，而不是在节点本身上显式控制。 如果密码套件与下面列出的某个套件不匹配，系统会拒绝传入的客户端连接。

### <a name="cipher-suite-supported"></a>支持的密码套件

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>后续步骤

- [Azure Database for MySQL 的连接库](concepts-connection-libraries.md)
- 了解如何[配置 SSL](howto-configure-ssl.md)
- 了解如何[配置 TLS](howto-tls-configurations.md)
