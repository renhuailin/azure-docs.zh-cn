---
title: 在 Azure SQL 数据库中规划 Intel SGX enclaves 和证明
description: 在 Azure SQL 数据库中计划安全 enclaves 的 Always Encrypted 部署。
keywords: 加密数据，sql 加密，数据库加密，敏感数据，Always Encrypted，安全 enclaves，SGX，证明
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 4448ce051b0c9e73865e8057cc4f224c9cbeb571
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732738"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>在 Azure SQL 数据库中规划 Intel SGX enclaves 和证明

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 对于 Azure SQL 数据库，具有 secure enclaves 的 Always Encrypted 当前提供 **公共预览版**。

在 Azure SQL 数据库中[使用 secure enclaves Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves)使用 intel [SGX) Enclaves (Intel 软件防护扩展](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/)，并需要[Microsoft Azure 证明](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation)。

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>在 Azure SQL 数据库中规划 Intel SGX

Intel SGX 是基于硬件的受信任执行环境技术。 Intel SGX 适用于使用 [vCore 模型](service-tiers-vcore.md) 和 [DC 系列](service-tiers-vcore.md?#dc-series) 硬件生成的数据库。 因此，为了确保你可以在数据库中使用安全 enclaves 的 Always Encrypted，需要在创建数据库时选择 DC 系列硬件生成，或者可以更新现有数据库以使用 DC 系列硬件生成。

> [!NOTE]
> Intel SGX 不适用于 DC 系列以外的硬件代。 例如，Intel SGX 不适用于 Gen5 硬件，不适用于使用 [DTU 模型](service-tiers-dtu.md)的数据库。

> [!IMPORTANT]
> 在为数据库配置 DC 系列硬件生成之前，请检查 DC 系列的区域可用性，并确保你了解其性能限制。 有关详细信息，请参阅 [DC 系列](service-tiers-vcore.md#dc-series)。

## <a name="plan-for-attestation-in-azure-sql-database"></a>在 Azure SQL 数据库中规划证明

[Microsoft Azure 认证](../../attestation/overview.md) (预览版) 是证明受信任的执行环境 (TEEs) 的解决方案，其中包括使用 DC 系列硬件生成的 Azure SQL 数据库中的 Intel SGX enclaves。

若要在 Azure SQL 数据库中使用适用于证明 Intel SGX enclaves 的 Azure 证明，需执行以下操作：

1. 创建 [证明提供者](../../attestation/basic-concepts.md#attestation-provider) ，并使用证明策略进行配置。 

2. 向 Azure SQL 逻辑服务器授予对所创建的证明提供者的访问权限。

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>配置 SGX enclaves 和证明时的角色和职责

在 Azure SQL 数据库中配置你的环境以支持 Intel SGX enclaves 和认证 Always Encrypted 涉及到设置不同类型的组件： Microsoft Azure 证明、Azure SQL 数据库和触发 enclave 证明的应用程序。 具有以下角色之一的用户配置每种类型的组件：

- 证明管理员-在 Microsoft Azure 证明，创建证明提供者，编写证明策略，授予 Azure SQL 逻辑服务器对证明提供程序的访问权限，并将指向策略的证明 URL 共享到应用程序管理员。
- Azure SQL 数据库管理员-在数据库中通过选择 DC 系列硬件生成来启用 SGX enclaves，并向证明管理员提供需要访问证明提供者的 Azure SQL 逻辑服务器的标识。
- 应用程序管理员-用从证明管理员获得的证明 URL 配置应用程序。

在生产环境中（处理真实的敏感数据），组织在配置证明时务必遵守角色分离，在这种情况下，不同人员具有不同角色。 具体而言，如果在你的组织中部署 Always Encrypted 的目标是通过确保 Azure SQL 数据库管理员无法访问敏感数据来减小攻击面，则 Azure SQL 数据库管理员不应控制证明策略。

## <a name="next-steps"></a>后续步骤

- [为 Azure SQL 数据库启用 Intel SGX](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>另请参阅

- [教程：在 Azure SQL 数据库中开始使用具有安全 enclave 的 Always Encrypted](always-encrypted-enclaves-getting-started.md)