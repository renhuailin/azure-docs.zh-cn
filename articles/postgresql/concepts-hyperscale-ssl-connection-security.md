---
title: 传输层安全性 (TLS) - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 介绍了如何配置 Azure Database for PostgreSQL - 超大规模 (Citus) 和关联应用程序来正确使用 TLS 连接。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87071457"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>在 Azure Database for PostgreSQL - 超大规模 (Citus) 中配置 TLS
超大规模 (Citus) 协调器节点要求客户端应用程序进行传输层安全性 (TLS) 连接。 通过在数据库服务器与客户端应用程序之间强制执行 TLS，可以在传输过程中保持数据的机密性。 下面描述的额外验证设置也可以防止“中间人”攻击。

## <a name="enforcing-tls-connections"></a>强制实施 TLS 连接
应用程序使用“连接字符串”来标识连接的目标数据库和设置。 不同的客户端需要不同的设置。 若要查看常见客户端使用的连接字符串的列表，请参阅 Azure 门户中服务器组的“连接字符串”部分。

TLS 参数 `ssl` 和 `sslmode` 因连接器的功能而异（例如 `ssl=true`、`sslmode=require` 或 `sslmode=required`）。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>确保应用程序或框架支持 TLS 连接
某些应用程序框架没有为 PostgreSQL 连接默认启用 TLS。 但是，如果没有安全连接，应用程序将无法连接到超大规模 (Citus) 协调器节点。 请查阅应用程序文档，了解如何启用 TLS 连接。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>需要证书验证才可启用 TLS 连接性的应用程序
在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件 (.cer) 生成的本地证书文件才能实现安全连接。 用于连接到 Azure Database for PostgreSQL - 超大规模 (Citus) 的证书位于 https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem 。 下载证书文件并将其保存到首选位置。

> [!NOTE]
>
> 若要检查证书的真伪，可以使用 OpenSSL 命令行工具来验证其 SHA-256 指纹：
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>使用 psql 进行连接
下面的示例展示了如何使用 psql 命令行实用工具来连接到超大规模 (Citus) 协调器节点。 使用 `sslmode=verify-full` 连接字符串设置来强制执行 TLS 证书验证。 将本地证书文件路径传递给 `sslrootcert` 参数。

下面是 psql 连接字符串示例：
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 确认传递给 `sslrootcert` 的值与你保存的证书的文件路径匹配。

## <a name="next-steps"></a>后续步骤
使用 [Azure Database for PostgreSQL - 超大规模 (Citus) 中的防火墙规则](concepts-hyperscale-firewall-rules.md)进一步提高安全性。
