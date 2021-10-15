---
title: Azure Database for PostgreSQL 灵活服务器中使用 TLS/SSL 的加密连接
description: 有关如何在 Azure Database for PostgreSQL 灵活服务器中使用 TLS/SSL 进行连接的说明和信息。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 065ffe56e2868cce00bd92c612beb987d9372e38
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388786"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 灵活服务器中使用传输层安全性的加密连接

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

Azure Database for PostgreSQL 灵活服务器支持使用传输层安全性 (TLS)（以前称为安全套接字层 (SSL)）将客户端应用程序连接到 PostgreSQL 服务。 TLS 是一种行业标准协议，可确保在数据库服务器与客户端应用程序之间实现加密的网络连接，使你能够满足合规性要求。

Azure Database for PostgreSQL 灵活服务器支持使用传输层安全性 (TLS 1.2+) 的加密连接，并且所有使用 TLS 1.0 和 TLS 1.1 的传入连接都将被拒绝。 为所有灵活服务器启用了强制实施 TLS 连接的功能。 

>[!Note]
> 默认情况下，将强制实施客户端与服务器之间的安全连接。 若要禁用 TLS/SSL 以连接到灵活的服务器，可以将服务器参数 `require_secure_transport` 更改为 `OFF`。 

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>需要证书验证才能进行 TLS/SSL 连接的应用程序
在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件生成的本地证书文件才能实现安全连接。 Azure Database for PostgreSQL 灵活服务器使用 DigiCert 全局根 CA。 从 [DigiCert 全局根 CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) 下载此证书（通过 SSL 通信需要它），并将证书文件保存到首选位置。 例如，本教程使用 `c:\ssl`。


### <a name="connect-using-psql"></a>使用 psql 进行连接
如果使用“专用访问(VNet 集成)”创建了灵活服务器，需要从与服务器相同的 VNet 中的资源连接到服务器。 可以创建虚拟机并将其添加到使用灵活服务器创建的 VNet 中。

如果使用“公共访问(允许的 IP 地址)”创建了灵活服务器，可以将本地 IP 地址添加到服务器上的防火墙规则列表中。

以下示例演示如何使用 psql 命令行接口连接到服务器。 使用 `sslmode=verify-full` 连接字符串设置强制实施 TLS/SSL 证书验证。 将本地证书文件路径传递给 `sslrootcert` 参数。

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> 确认传递给 `sslrootcert` 的值与你保存的证书的文件路径匹配。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>确保应用程序或框架支持 TLS 连接

某些使用 PostgreSQL 作为其数据库服务的应用程序框架在安装期间默认不启用 TLS。 你的 PostgreSQL 服务器强制实施 TLS 连接，但如果应用程序未配置 TLS，则应用程序可能无法连接到数据库服务器。 请查阅应用程序文档，了解如何启用 TLS 连接。

## <a name="next-steps"></a>后续步骤
- [使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-cli.md)。
- 详细了解 [Azure Database for PostgreSQL 灵活服务器中的网络](./concepts-networking.md)
- 详细了解 [Azure Database for PostgreSQL 灵活服务器防火墙规则](./concepts-networking.md#public-access-allowed-ip-addresses)
