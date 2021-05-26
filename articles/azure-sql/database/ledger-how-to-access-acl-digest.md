---
title: 如何访问 Azure 机密账本 (ACL) 中存储的摘要
description: 如何在 Azure SQL 数据库账本中访问 Azure 机密账本 (ACL) 中存储的摘要
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: e5baf12de52543280d294fc68f326f53e045f70d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385329"
---
# <a name="how-to-access-the-digests-stored-in-acl"></a>如何访问 ACL 中存储的摘要

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版。

本文介绍如何访问 [Azure 机密账本 (ACL)](/azure/confidential-ledger/) 中存储的 [Azure SQL 数据库账本](ledger-overview.md)摘要，以获得端到端的安全性和完整性保证。 本文介绍如何访问和验证存储信息的完整性。

## <a name="prerequisites"></a>先决条件

- Python 2.7、3.5.3 或更高版本
- 具有启用了账本的现有 Azure SQL 数据库。 如果尚未创建 Azure SQL 数据库，请参阅[快速入门：创建启用了账本的 Azure SQL 数据库](ledger-create-a-single-database-with-ledger-enabled.md)。
- [适用于 Python 的 Azure 机密账本客户端库](https://github.com/Azure/azure-sdk-for-python/blob/b42651ae4791aca8c9fbe282832b81badf798aa9/sdk/confidentialledger/azure-confidentialledger/README.md#create-a-client)
- [Azure 机密账本](/azure/confidential-ledger/)的运行实例。

## <a name="how-does-the-integration-work"></a>集成的工作原理

Azure SQL 服务器会定期计算[账本数据库](ledger-overview.md#ledger-database)摘要，并将其存储在 Azure 机密账本中。 用户可随时从 Azure 机密账本中下载摘要，将其与 Azure SQL 数据库账本中存储的摘要进行比较，从而验证完整性。 以下步骤将说明这一过程。

## <a name="step-1---find-the-digest-location"></a>步骤 1 - 查找摘要位置

> [!NOTE]
> 如果使用了多个 Azure 机密账本实例来存储摘要，则查询将返回多行。 为每一行重复步骤 2-6，从 Azure 机密账本的所有实例中下载摘要。

使用 [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)，运行以下查询。 输出将显示存储摘要的 Azure 机密账本实例的终结点。

```sql
SELECT * FROM sys.database_ledger_digest_locations WHERE path like '%.confidential-ledger.azure.com%
```

## <a name="step-2---determine-the-subledgerid"></a>步骤 2 - 确定 Subledgerid

我们对查询输出中路径列的值感兴趣。 它包含两个部分，即 `host name` 和 `subledgerid`。 例如，在 URL `https://contoso-ledger.confidential-ledger.azure.com/sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000` 中，`host name` 是 `https://contoso-ledger.confidential-ledger.azure.com`，`subledgerid` 是 `sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000`。 我们将在步骤 4 中使用它来下载摘要。

## <a name="step-3---obtain-an-azure-ad-token"></a>步骤 3 - 获取 Azure AD 令牌

Azure 机密账本 API 接受 Azure Active Directory (Azure AD) 持有者令牌作为调用方标识。 此标识在预配过程中需要通过 Azure 资源管理器访问 ACL。 在 SQL 数据库中启用了账本的用户会自动获得 Azure 机密账本的管理员权限。 若要获取令牌，用户需使用 [Azure CLI](/cli/azure/install-azure-cli) 向 Azure 门户中使用的同一帐户进行身份验证。 用户通过身份验证后，便可使用 [DefaultAzureCredentials()](/dotnet/api/azure.identity.defaultazurecredential) 检索持有者令牌并调用 Azure 机密账本 API。

使用具有 ACL 访问权限的标识登录到 Azure AD。

```azure-cli
az login
```

检索持有者令牌。

```python
from azure.identity import DefaultAzureCredential
credential = DefaultAzureCredential()
```

## <a name="step-4---download-the-digests-from-azure-confidential-ledger"></a>步骤 4 - 从 Azure 机密账本下载摘要

以下 Python 脚本将从 Azure 机密账本下载摘要。 此脚本使用[适用于 Python 的 Azure 机密账本客户端库](https://github.com/Azure/azure-sdk-for-python/blob/b42651ae4791aca8c9fbe282832b81badf798aa9/sdk/confidentialledger/azure-confidentialledger/README.md#create-a-client)。

```python
from azure.identity import DefaultAzureCredential
from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

ledger_id = "contoso-ledger"
identity_server_url = "https://identity.confidential-ledger.core.azure.com"
sub_ledger_id = "sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000"
ledger_host_url = f"https://{ledger_id}.confidential-ledger.azure.com"
initial_path = f"/app/transactions?api-version=0.1-preview&subLedgerId={sub_ledger_id}"

identity_client = ConfidentialLedgerIdentityServiceClient(identity_server_url)
network_identity = identity_client.get_ledger_identity(
    ledger_id=ledger_id
)

ledger_tls_cert_file_name = f"{ledger_id}_certificate.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)

credential = DefaultAzureCredential()
ledger_client = ConfidentialLedgerClient(
    endpoint=ledger_host_url, 
    credential=credential,
    ledger_certificate_path=ledger_tls_cert_file_name
)

ranged_result = ledger_client.get_ledger_entries(
    sub_ledger_id=sub_ledger_id
)

entries = 0

for entry in ranged_result:
    entries += 1
    print(f"\nTransaction id {entry.transaction_id} contents: {entry.contents}")

if entries == 0:
    print("\n***No digests are found for the supplied SubledgerID.")
else:
    print("\n***No more digests were found for the supplied SubledgerID.")
```

## <a name="step-5---download-the-digests-from-the-sql-server"></a>步骤 5 - 从 SQL Server 下载摘要

> [!NOTE]
> 该方法可用于确认 Azure SQL 数据库账本中存储的哈希值在一段时间内是否未发生变化。 如需查看完整的 Azure SQL 数据库账本完整性审核，请参阅[如何验证账表以检测篡改](ledger-verify-database.md)。

使用 [SSMS](/sql/ssms/download-sql-server-management-studio-ssms)，运行以下查询。 查询将从 Genesis 返回块的摘要。

```sql
SELECT * FROM sys.database_ledger_blocks
```

## <a name="step-6---comparison"></a>步骤 6 - 比较

将从 Azure 机密账本中检索到的摘要与从 SQL 数据库（使用 `block_id` 作为键）返回的摘要进行比较。 例如，`block_id` = `1` 的摘要是 `block_id`= `2` 行中 `previous_block_hash` 列的值。 同样，对于 `block_id` = `3`，它是 `block_id` = `4` 行中 `previous_block_id` 列的值。 若哈希值不一致，则表明数据可能遭到篡改。

若怀疑数据遭到篡改，请参阅[如何验证账表以检测篡改](ledger-verify-database.md)，对 Azure SQL 数据库账本执行完整审核。

## <a name="next-steps"></a>后续步骤

- [Azure SQL 数据库账本概述](ledger-overview.md)
- [数据库账本](ledger-database-ledger.md)
- [摘要管理和数据库验证](ledger-digest-management-and-database-verification.md)
- [仅追加账本表](ledger-append-only-ledger-tables.md)
- [可更新账表](ledger-updatable-ledger-tables.md)
- [如何验证账表以检测篡改](ledger-verify-database.md)