---
title: Azure SQL 数据库和 SQL 托管实例的证书轮换
description: 了解即将推出的会影响 Azure SQL 数据库和 Azure SQL 托管实例的根证书更改
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: 96367b143711c4ec5f3f8d609f048c72c6fded16
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590845"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>了解 Azure SQL 数据库和 SQL 托管实例的根 CA 更改

Azure SQL 数据库和 SQL 托管实例将更改通过 SSL 启用的客户端应用程序/驱动程序的根证书，该证书用于建立安全的 TDS 连接。 根据标准维护和安全最佳做法的要求，[当前根证书](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)已设置为在 2020 年 10 月 26 日过期。 本文更详细地介绍了即将推出的更改、会受影响的资源，以及确保应用程序始终与数据库服务器连接所需执行的步骤。

## <a name="what-update-is-going-to-happen"></a>打算进行什么样的更新？

[证书颁发机构 (CA) 浏览器论坛](https://cabforum.org/)最近发布的报告表明，由 CA 供应商颁发的多个证书不符合标准。

根据行业的合规性要求，CA 供应商已开始吊销不符合标准的 CA 的 CA 证书，要求服务器使用由符合标准的 CA 颁发的证书，并由这些符合标准的 CA 颁发的 CA 证书进行签名。 由于 Azure SQL 数据库和 SQL 托管实例当前使用其中一个不符合标准的证书，客户端应用程序使用这些证书来验证其 SSL 连接，因此，我们需要确保采取适当的措施（如下所述），以最大程度地减少对 Azure SQL 服务器的潜在影响。

新证书将从 2020 年 10 月 26 日开始使用。 如果在从 SQL 客户端连接时使用服务器证书的完整验证 (TrustServerCertificate=true)，则需要确保 SQL 客户端能够在 2020 年 10 月 26 日之前验证新的根证书。

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>如何知道我的应用程序是否可能会受影响？

使用 SSL/TLS 并验证根证书的所有应用程序都需要更新根证书才能连接到 Azure SQL 数据库和 SQL 托管实例。 

如果你当前未使用 SSL/TLS，则不会影响应用程序的可用性。 可以通过查看连接字符串来验证客户端应用程序是否正在尝试验证根证书。 如果 TrustServerCertificate 显式设置为 true，则你不会受影响。

像大多数驱动程序一样，如果你的客户端驱动程序使用操作系统证书存储，并且你的操作系统会定期维护，则此更改很可能不会影响你，因为我们要切换到的根证书应当已在受信任的根证书存储中可用。 检查 Baltimore CyberDigiCert GlobalRoot G2 并验证它是否存在。

如果你的客户端驱动程序利用本地文件证书存储，为避免应用程序的可用性因证书被意外吊销而中断，或要更新已吊销的证书，请参阅 [**我需要做什么来维护连接**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)部分。

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>我需要做什么来维护连接

若要避免应用程序的可用性因证书被意外吊销而中断，或要更新已吊销的证书，请执行以下步骤。

*   从以下链接下载 Baltimore CyberTrust 根和 DigiCert GlobalRoot G2 根 CA：
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   生成组合的 CA 证书存储，其中同时包含 **BaltimoreCyberTrustRoot** 和 **DigiCertGlobalRootG2** 证书。

## <a name="what-can-be-the-impact"></a>影响有哪些？
如果如此处所述验证服务器证书，则可能会中断应用程序的可用性，因为数据库将无法访问。 根据你的应用程序，你可能会收到各种错误消息，其中包括但不限于：
*   证书/已吊销的证书无效
*   连接超时
*   错误（如果适用）

## <a name="frequently-asked-questions"></a>常见问题

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>如果我不使用 SSL/TLS，是否仍需要更新根 CA？
如果不使用 SSL/TLS，则不需要执行与此更改相关的任何操作。 不过，你仍然应该制定一个计划，以便开始使用最新的 TLS 版本，因为我们计划在不久的将来实施 TLS。

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>如果在 2020 年 10 月 26 日之前未更新根证书，会发生什么情况？
如果在 2020 年 11 月 30 日之前未更新根证书，则通过 SSL/TLS 进行连接并对根证书进行验证的应用程序将无法与 Azure SQL 数据库和 SQL 托管实例通信，并且应用程序会遇到与 Azure SQL 数据库和 SQL 托管实例的连接问题。

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>是否需要为此更改对维护停机时间进行计划？<BR>
否。 由于此处的更改仅发生在要连接到服务器的客户端，因此不需要维护停机时间来进行此更改。

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>如果在 2020 年 10 月 26 日之前无法安排停机时间来进行此更改，该怎么办？
由于用于连接到服务器的客户端需要如[此处](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)的修复部分所述更新证书信息，因此在这种情况下，服务器不需要停机。

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>如果我在 2020 年 11 月 30 日之后创建新的服务器，我是否会受影响？
对于在 2020 年 10 月 26 日之后创建的服务器，可以使用应用程序的新颁发的证书通过 SSL 进行连接。

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Microsoft 更新其证书的频率是多少？或者说过期策略是怎样的？
Azure SQL 数据库和 SQL 托管实例使用的这些证书是由受信任的证书颁发机构 (CA) 提供的。 因此，Azure SQL 数据库和 SQL 托管实例对这些证书的支持与 CA 对这些证书的支持相关联。 但是，这些预定义证书中可能存在无法预料的 bug，这些 bug 需要尽早进行修复，正如此示例一样。

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-primary-server-or-all-the-read-replicas"></a>如果我使用的是读取副本，是否只需在主服务器或所有读取副本上执行此更新？
由于此更新是客户端更改，因此，如果客户端过去从副本服务器读取数据，则我们还需要对这些客户端应用更改。 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>是否有服务器端查询可用来验证是否正在使用 SSL？
由于此配置是客户端的，因此不能在服务器端使用此信息。

### <a name="what-if-i-have-further-questions"></a>如果我有其他问题，该怎么办？
如果你有支持计划并需要技术帮助，请创建 Azure 支持请求，详见[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。