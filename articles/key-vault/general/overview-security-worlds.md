---
title: Azure Key Vault 安全体系 | Microsoft Docs
description: Azure Key Vault 是一项多租户服务。 它在每个 Azure 区域使用 HSM 池。 同一地理区域中的所有区域都共享相同的加密边界。
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0753108df52f0dcaea1d6c79eab266eeb000889a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747224"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault 安全体系和地理边界

Azure 产品可用于许多 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)，每个 Azure 地理位置包含一个或多个区域。 例如，欧洲地区包含两个区域（北欧和西欧），但巴西地区只有一个区域 - 巴西南部。

Azure Key Vault 是一项多租户服务，使用一个硬件安全模块 (HSM) 池。 一个地理位置中的所有 HSM 共享相同的加密边界，称为“安全体系”。 每个地理位置对应一个安全体系，反之亦然。

美国东部和美国西部共享同一个安全体系，因为它们属于一个地理位置（美国）。 同样，位于日本的所有 Azure 区域共享一个安全体系，位于澳大利亚的所有 Azure 区域也是如此，以此类推。

>[!NOTE]
> 例外情况是 US DoD 东部和 US DoD 中部具有自己的安全体系。

## <a name="backup-and-restore-behavior"></a>备份和还原行为

只要满足以下两个条件，在一个 Azure 区域中对密钥保管库中的密钥执行的备份可以还原到另一个 Azure 区域中的密钥保管库：

- 两个 Azure 区域属于相同的地理位置。
- 两个密钥保管库属于同一个 Azure 订阅。

例如，只能将对“印度西部”密钥保管库中的密钥执行的备份还原到“印度”这个地理位置（印度西部、印度中部或印度南部区域）中的相同订阅内的另一个密钥保管库。

## <a name="next-steps"></a>后续步骤

- 请参阅 [Microsoft 产品（按区域）](https://azure.microsoft.com/regions/services/)
