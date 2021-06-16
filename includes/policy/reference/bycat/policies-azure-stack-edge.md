---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ae13ed8f850b7432fa72f23bfbd7b29fe5b80df7
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112019313"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stack Edge 设备应使用双重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |若要在设备上保护静态数据，请确保静态数据已双重加密，已控制对数据的访问，并在设备停用后从数据磁盘安全地清除数据。 双重加密使用两层加密：数据卷上的 BitLocker XTS-AES 256 位加密和硬盘驱动器上的内置加密。 有关详细信息，请参阅特定 Stack Edge 设备的安全概述文档。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
