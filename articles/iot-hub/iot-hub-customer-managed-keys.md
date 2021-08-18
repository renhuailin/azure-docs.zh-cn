---
title: 使用客户管理的密钥进行 Azure IoT 中心静态数据加密 | Microsoft Docs
description: 使用客户管理的密钥进行 Azure IoT 中心静态数据加密
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: asrastog
ms.openlocfilehash: 4dd3ee01504c2777ad72e32e11932b3b63d07448
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515537"
---
# <a name="encryption-of-azure-iot-hub-data-at-rest-using-customer-managed-keys"></a>使用客户管理的密钥进行 Azure IoT 中心静态数据加密

IoT 中心支持使用客户管理的密钥 (CMK) 对静态数据进行加密，该密钥也称为“创建自己的密钥”(BYOK)。 Azure IoT 中心对写入数据中心的静态数据和传输中的数据进行加密；这些数据在读取时加密，在写入时解密。 

默认情况下，IoT 中心使用 Microsoft 管理的密钥来加密数据。 使用 CMK，可以在默认加密的基础上再添加一层加密，还可以选择使用密钥加密密钥（通过 [Azure密钥保管库](https://azure.microsoft.com/services/key-vault/)管理）来加密静态数据。 这使你能够灵活创建、轮换、禁用和撤销访问控制权。 如果为 IoT 中心配置了 BYOK，我们还提供双重加密，可提供额外一层保护，同时仍可通过 Azure Key Vault 控制加密密钥。

此功能需要创建新的 IoT 中心（基本层或标准层）。 若要试用此功能，请通过 [Microsoft 支持](https://azure.microsoft.com/support/create-ticket/)人员与我们联系。 联系 Microsoft 支持人员时，请提供你的公司名称和订阅 ID。

## <a name="next-steps"></a>后续步骤

* [什么是 IoT 中心？](./about-iot-hub.md)

* [详细了解 Azure 密钥保管库](../key-vault/general/overview.md)
