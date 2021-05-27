---
title: Azure SQL 数据库账本概述
description: Azure SQL 数据库账本概述
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: e74b4ed5b54a27b13768c19878331a9779bb9dc6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110456836"
---
# <a name="azure-sql-database-ledger"></a>Azure SQL 数据库账本

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前以公共预览版提供。

对于管理财务、医疗或其他敏感数据的所有组织来说，围绕存储在数据库系统中的数据完整性建立信任一直是一个长期存在的问题。 [Azure SQL 数据库](sql-database-paas-overview.md)的账本功能在数据库中提供篡改证据功能，从而能够以加密方式向其他方（如审核员或其他业务参与方）证明你的数据未被篡改。

账本有助于保护数据免受任何攻击者或高特权用户的攻击，包括数据库管理员 (DBA) 以及系统和云管理员。 与传统的账本一样，保留历史数据，这样一来，如果某个行在数据库中进行更新，则其以前的值在历史记录表中得到维护和保护。 账本提供了一段时间内对数据库所做的所有更改的历史记录。 账本和历史数据以透明方式进行管理，无需更改任何应用程序即可提供保护。 历史数据以关系形式进行维护，以支持用于审核、取证和其他目的的 SQL 查询。 账本在保持 Azure SQL 数据库的功能、灵活性和性能的同时，提供了加密数据完整性的保证。

:::image type="content" source="media/ledger/ledger-table-architecture.png" alt-text="账本表体系结构":::

## <a name="use-case-for-azure-sql-database-ledger"></a>Azure SQL 数据库账本的用例 

### <a name="streamlining-audits"></a>简化审核

任何生产系统的价值都基于信任系统正在使用和生产的数据的能力。 如果数据库中的数据被恶意用户篡改，则可能对依赖该数据的业务流程产生灾难性的结果。 保持对数据的信任需要结合实现适当的安全控制来减少潜在攻击、备份和还原做法，以及彻底的灾难恢复过程。 确保这些做法落实到位，通常由外部参与方审核。 审核过程是非常耗时的活动。 审核需要现场检查实现的做法，如查看审核日志、检查身份验证和访问控制等。 尽管这些手动过程可能会造成潜在的安全漏洞，但它们无法提供有力证据来表明数据并未受到恶意篡改。 账本向审计员提供数据完整性的加密证明，这不仅有助于简化审核过程，还可以提供有关系统数据完整性的不可否认性。

### <a name="multi-party-business-processes"></a>多方业务流程

对于多个组织共享一个业务流程的系统（例如供应链管理系统），由于组织必须彼此共享状态，因此组织之间如何共享和和信任数据是一大挑战。 许多组织都转向传统区块链，例如以太坊或 Hyperledger Fabric，以数字方式转换其多方业务流程。 区块链是适用于多方网络的最佳解决方案，在这种网络中，参与网络的各方之间的信任度较低。 然而，许多这样的网络基本上都是集中式解决方案，对信任要求很高，而完全分布式基础结构是一个重量级的解决方案。 账本为这些网络提供了一种解决方案，参与者可以验证集中存储的数据的数据完整性，而不必担心网络共识在区块链网络中引入的复杂性和性能影响。

### <a name="trusted-off-chain-storage-for-blockchain"></a>适用于区块链的受信任的链外存储

在多方业务流程需要区块链网络的情况下，能在不影响性能的情况下查询区块链上的数据是一个挑战。 解决此问题的典型模式涉及将数据从区块链复制到链外存储，如数据库。 然而，一旦数据从区块链复制到数据库，区块链提供的数据完整性保证就会丢失。 账本提供区块链网络链外存储所需的数据完整性，确保整个系统完全信任数据。

## <a name="how-it-works"></a>工作原理

数据库接收的每个事务都使用哈希加密 (SHA-256)。 哈希函数使用事务值（包括事务中包含的行的哈希），以及前一个事务的哈希作为哈希函数的输入。 函数以加密方式将所有事务链接在一起，类似于区块链。 表示数据库状态的加密哈希（[数据库摘要](#database-digests)）定期生成，并存储在 Azure SQL 数据库外部的防篡改存储位置中。 存储位置的一个示例是 [Azure 存储不可变 Blob](../../storage/blobs/storage-blob-immutable-storage.md) 或 [Azure 机密账本](/azure/confidential-ledger/)。 数据库摘要稍后用于验证数据库的完整性，方法是将摘要中的哈希值与数据库中计算出的哈希值进行比较。 

账本功能以两种形式引入 Azure SQL 数据库中的表：

- [可更新账本表](ledger-updatable-ledger-tables.md)：用于更新和删除表中的行。
- [仅追加账本表](ledger-append-only-ledger-tables.md)：仅允许插入表。

“可更新账本表”和“仅追加账本表”都提供篡改证据和数字取证功能 。 如果要修正潜在的篡改事件，或向第三方证明提交给系统的事务是由授权用户进行的，那么应了解哪些用户提交的事务导致了对数据库的更改，这一点很重要。 账本功能使用户、其合作伙伴或审核员能够分析所有历史操作并检测潜在的篡改。 每个行操作都伴随着执行该操作的事务的 ID，从而允许用户检索有关事务执行时间和执行者标识的详细信息，并将其与此事务执行的其他操作关联起来。

应注意账本表的一些限制。 有关账本表的限制的详细信息，请参阅 [Azure SQL 数据库账本的限制](ledger-limits.md)。

### <a name="ledger-database"></a>账本数据库

账本数据库是一个数据库，其中所有的用户数据都是防篡改的，并存储在账本表中。 一个账本数据库只能包含账本表，每个表默认创建为可更新的账本表。 对于需要保护所有数据的完整性的应用程序，账本数据库为其提供了一种易于使用的解决方案。 

### <a name="updatable-ledger-tables"></a>可更新账本表

对于希望对数据库中的表进行更新和删除的应用程序模式，例如记录系统 (SOR) 应用程序，那么[可更新账本表](ledger-updatable-ledger-tables.md)是理想选择。 这意味着，应用程序的现有数据模式不需要更改即可启用账本功能。  

[可更新账本表](ledger-updatable-ledger-tables.md)会在执行更新或删除的事务发生时跟踪数据库中任何行的更改历史记录。 可更新账本表是带有系统版本的表，它包含对另一个具有镜像架构的表的引用。 每当更新或删除了账本表中的某行后，系统都将使用此表来自动存储该行的先前版本。 另一种表称为历史记录表。 创建可更新账本表时，将自动创建历史记录表。 可更新账本表中包含的值及其相应的历史记录表提供了一段时间内数据库值的历史记录。 为了轻松地查询数据库的此历史记录，将创建一个系统生成的账本视图，它将联接可更新账本表和历史记录表。

若要详细了解如何创建和使用可更新账本表，请参阅[创建和使用可更新账本表](ledger-how-to-updatable-ledger-tables.md)。

### <a name="append-only-ledger-tables"></a>仅追加账本表

[仅追加账本表](ledger-append-only-ledger-tables.md)非常适合仅插入的应用程序模式，例如安全信息和事件管理 (SEIM) 应用程序。 仅追加账本表在应用程序编程接口 (API) 级别阻止更新和删除，进一步防止特权用户（例如系统管理员和 DBA）篡改数据。 由于系统只允许插入，因此仅追加账本表没有对应的历史记录表，因为没有要捕获的历史记录。 与可更新账本表一样，创建账本视图可提供对将行插入仅追加表的事务以及执行插入的用户的见解。

若要详细了解如何创建和使用仅追加账本表，请参阅[创建和使用仅追加账本表](ledger-how-to-append-only-ledger-tables.md)。

### <a name="database-ledger"></a>数据库账本

数据库账本由系统表组成，这些表存储系统中处理的事务的加密哈希。 由于事务是数据库引擎的[原子性](/windows/win32/cossdk/acid-properties)单位，因此这是在数据库账本中捕获的工作单元。 具体而言，当事务提交时，由账本表中的事务修改的任何行的 SHA-256 哈希以及事务的一些元数据（例如执行事务的用户的标识及其提交时间戳）会作为事务条目追加到数据库账本中。 每 30 秒，数据库处理的事务会使用 Merkle 树数据结构进行 SHA-256 哈希处理，从而生成根哈希。 此行为会形成一个块，然后使用块的根哈希以及上一个块的根哈希作为哈希函数的输入对 SHA-256 进行哈希处理，从而形成一个区块链。

有关数据库账本详细信息，请参阅[数据库账本](ledger-database-ledger.md)。

### <a name="database-digests"></a>数据库摘要

数据库账本中最新块的哈希称为数据库摘要，并表示生成块时数据库中所有账本表的状态。 块形成后，它所关联的数据库摘要随后将发布并存储在 Azure SQL 数据库之外的防篡改存储中。 由于数据库摘要表示在生成数据库时数据库的状态，因此保护摘要免受篡改至关重要。 有权修改摘要的攻击者能够篡改数据库中的数据，生成表示被篡改的数据库的哈希，然后修改摘要以表示该块中事务的更新哈希。 通过账本，可以在 [Azure 存储不可变 blob](../../storage/blobs/storage-blob-immutable-storage.md) 或 [Azure 机密账本](/azure/confidential-ledger/)中自动生成并存储数据库摘要，以防止篡改。 或者，用户可以手动生成数据库摘要，并将其存储在所选位置。 数据库摘要用于稍后验证存储在账本表中的数据是否未被篡改。

有关数据库摘要的详细信息，请参阅[摘要管理和数据库验证](ledger-digest-management-and-database-verification.md)。

### <a name="ledger-verification"></a>账本验证

此账本功能不允许用户修改账本的内容。 但是，控制计算机的攻击者或系统管理员可以绕过所有系统检查，直接篡改数据。 例如，攻击者或系统管理员可以编辑存储中的数据库文件。 账本无法防止此类攻击，但可保证在验证账本数据时会检测到任何篡改。 账本验证过程使用先前生成的一个或多个数据库摘要作为输入，并根据账本表的当前状态，重新计算存储在数据库账本中的哈希值。 如果计算的哈希与输入摘要不匹配，则验证失败，表明数据已被篡改，并报告检测到的所有不一致情况。

由于账本验证会重新计算数据库中事务的所有哈希，因此对于包含大量数据的数据库，此过程可能是一个资源密集型过程。 只有在用户需要验证其数据库的完整性时，才应运行账本验证，而不是以连续方式运行。 理想情况下，只有在托管数据的组织通过审核并且需要向另一方提供有关其数据完整性的加密证据时，才应运行账本验证。 为了降低验证成本，账本公开用于验证各个账本表的选项，或仅验证账本的子集。

有关账本验证的详细信息，请参阅[摘要管理和数据库验证](ledger-digest-management-and-database-verification.md)。

## <a name="next-steps"></a>后续步骤
 
- [快速入门：创建启用了账本的 Azure SQL 数据库](ledger-create-a-single-database-with-ledger-enabled.md)
- [可更新账本表](ledger-updatable-ledger-tables.md)   
- [仅追加账本表](ledger-append-only-ledger-tables.md)   
- [数据库账本](ledger-database-ledger.md)   
- [摘要管理和数据库验证](ledger-digest-management-and-database-verification.md)   
- [Azure SQL 数据库账本限制](ledger-limits.md)
- [创建和使用可更新账本表](ledger-how-to-updatable-ledger-tables.md)
- [创建和使用仅追加账本表](ledger-how-to-append-only-ledger-tables.md)
- [如何访问存储在 Azure 机密账本 (ACL) 中的摘要](ledger-how-to-access-acl-digest.md)
- [如何验证账本表以检测篡改](ledger-verify-database.md)
