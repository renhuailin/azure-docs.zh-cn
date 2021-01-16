---
title: 为 Azure SQL 数据库启用 Intel SGX
description: 通过选择支持 SGX 的硬件生成，了解如何通过 Azure SQL 数据库中的安全 enclaves 启用 Intel SGX for Always Encrypted。
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
ms.openlocfilehash: 4c77103dbb043ef9d6af9a4078b3e574ab5f953f
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253336"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>为 Azure SQL 数据库启用 Intel SGX 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 对于 Azure SQL 数据库，具有 secure enclaves 的 Always Encrypted 当前提供 **公共预览版**。

在 Azure SQL 数据库中[使用 secure enclaves Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves)使用 intel [SGX) Enclaves (Intel 软件防护扩展](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/)。 要使 Intel SGX 可用，数据库必须使用 [vCore 模型](service-tiers-vcore.md) 和 [DC 系列](service-tiers-vcore.md#dc-series) 硬件生成。

配置 DC 系列硬件生成以启用 Intel SGX enclaves 是 Azure SQL 数据库管理员的责任。 请参阅 [配置 SGX enclaves 和证明时的角色和职责](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)。

> [!NOTE]
> Intel SGX 不适用于 DC 系列以外的硬件代。 例如，Intel SGX 不适用于 Gen5 硬件，不适用于使用 [DTU 模型](service-tiers-dtu.md)的数据库。

> [!IMPORTANT]
> 在为数据库配置 DC 系列硬件生成之前，请检查 DC 系列的区域可用性，并确保你了解其性能限制。 有关详细信息，请参阅 [DC 系列](service-tiers-vcore.md#dc-series)。

有关如何将新的或现有的数据库配置为使用特定的硬件生成的详细说明，请参阅 [选择硬件生成](service-tiers-vcore.md#selecting-a-hardware-generation)。
   
## <a name="next-steps"></a>后续步骤

- [配置 Azure SQL 数据库服务器的 Azure 证明](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>另请参阅

- [教程：在 Azure SQL 数据库中利用安全 enclaves 入门 Always Encrypted](always-encrypted-enclaves-getting-started.md)