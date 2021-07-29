---
title: 为 Always Encrypted 启用 Intel SGX
description: 了解如何通过选择支持 SGX 的硬件代，为 Azure SQL 数据库中的具有安全 Enclave 的 Always Encrypted 启用 Intel SGX。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 1bfab8547f3c6cc010036170c082eecdb4e335ae
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411551"
---
# <a name="enable-intel-sgx-for-always-encrypted-for-your-azure-sql-database"></a>为 Azure SQL 数据库的 Always Encrypted 启用 Intel SGX 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库的具有安全 Enclave 的 Always Encrypted 目前提供公共预览版。

Azure SQL 数据库中[具有安全 Enclave 的 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) 使用 [Intel Software Guard Extensions (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) Enclave。 若要使 Intel SGX 可用，数据库必须使用 [vCore 模型](service-tiers-vcore.md)和 [DC 系列](service-tiers-sql-database-vcore.md#dc-series)硬件代。

Azure SQL 数据库管理员应负责配置 DC 系列硬件代以启用 Intel SGX Enclave。 请参阅[配置 SGX Enclave 和证明时的角色和职责](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)。

> [!NOTE]
> Intel SGX 不适用于 DC 系列以外的硬件代。 例如，Intel SGX 不适用于 Gen5 硬件，也不适用于使用 [DTU 模型](service-tiers-dtu.md)的数据库。

> [!IMPORTANT]
> 在为数据库配置 DC 系列硬件代之前，请检查 DC 系列的区域可用性，并确保你了解其性能限制。 有关更多信息，请参见 [DC 系列](service-tiers-sql-database-vcore.md#dc-series)。

有关如何配置新的或现有数据库以使用特定硬件代的详细说明，请参阅[选择硬件代](service-tiers-sql-database-vcore.md#selecting-a-hardware-generation)。
   
## <a name="next-steps"></a>后续步骤

- [为 Azure SQL 数据库服务器配置 Azure 证明](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>另请参阅

- [教程：在 Azure SQL 数据库中开始使用具有安全 enclave 的 Always Encrypted](always-encrypted-enclaves-getting-started.md)