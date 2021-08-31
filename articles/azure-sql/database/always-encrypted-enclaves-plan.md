---
title: 在 Azure SQL Database 中规划 Intel SGX enclave 和证明
description: 在 Azure SQL 数据库中规划部署具有安全 enclave 的 Always Encrypted。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.openlocfilehash: bed170c4dbf61006c7d2aca14117f8946563f357
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727271"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>在 Azure SQL Database 中规划 Intel SGX enclave 和证明

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL 数据库中[具有安全 enclave 的 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) 可使用 [Intel Software Guard Extensions (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) enclave，并需要 [Microsoft Azure 证明](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation)。

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>在 Azure SQL 数据库中规划 Intel SGX

Intel SGX 是基于硬件的受信任执行环境技术。 Intel SGX 适用于使用 [vCore 模型](service-tiers-sql-database-vcore.md)和 [DC 系列](service-tiers-sql-database-vcore.md?#dc-series)硬件生成的数据库。 因此，为了确保可以在数据库中使用具有安全 enclave 的 Always Encrypted，需要在创建数据库时选择 DC 系列硬件生成，也可以更新现有数据库以使用 DC 系列硬件生成。

> [!NOTE]
> Intel SGX 不适用于 DC 系列以外的硬件代。 例如，Intel SGX 不适用于 Gen5 硬件，也不适用于使用 [DTU 模型](service-tiers-dtu.md)的数据库。

> [!IMPORTANT]
> 在为数据库配置 DC 系列硬件生成之前，请检查 DC 系列的区域可用性，并确保你了解其性能限制。 有关详细信息，请参阅 [DC 系列](service-tiers-sql-database-vcore.md#dc-series)。

## <a name="plan-for-attestation-in-azure-sql-database"></a>在 Azure SQL 数据库中规划证明

[Microsoft Azure 证明](../../attestation/overview.md)是证明受信任执行环境 (TEE) 的解决方案，其中包括使用 DC 系列硬件生成的 Azure SQL 数据库中的 Intel SGX enclave。

若要在 Azure SQL 数据库中使 用Azure 证明来证明 Intel SGX enclave，需要创建[证明提供程序](../../attestation/basic-concepts.md#attestation-provider)，并使用 Microsoft 提供的证明策略进行配置。 请参阅[使用 Azure 证明为 Always Encrypted 配置证明](always-encrypted-enclaves-configure-attestation.md)

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>配置 SGX enclave 和证明时的角色和职责

在 Azure SQL 数据库中配置你的环境以支持 Intel SGX enclave 和认证 Always Encrypted 涉及到设置不同类型的组件： Microsoft Azure 证明、Azure SQL 数据库和触发 enclave 证明的应用程序。 具有以下角色之一的用户配置每种类型的组件：

- 证明管理员-在 Microsoft Azure 证明中创建证明提供程序，编写证明策略，授予 Azure SQL 逻辑服务器对证明提供程序的访问权限，并将指向策略的证明 URL 共享给应用程序管理员。
- Azure SQL 数据库管理员-在数据库中通过选择 DC 系列硬件生成来启用 SGX enclave，并向证明管理员提供访问证明提供程序所需的 Azure SQL 逻辑服务器的标识。
- 应用程序管理员-使用从证明管理员处获得的证明 URL 配置应用程序。

在生产环境中（处理真实的敏感数据），组织在配置证明时务必遵守角色分离，在这种情况下，不同人员具有不同角色。 特别是，如果在组织中部署 Always Encrypted 的目的是确保 Azure SQL 数据库管理员无法访问敏感数据，从而减少攻击外围应用，则 Azure SQL 数据库管理员不应控制证明策略。

## <a name="next-steps"></a>后续步骤

- [为 Azure SQL 数据库启用 Intel SGX](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>另请参阅

- [教程：在 Azure SQL 数据库中开始使用具有安全 enclave 的 Always Encrypted](always-encrypted-enclaves-getting-started.md)