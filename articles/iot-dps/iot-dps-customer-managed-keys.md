---
title: Azure 设备预配服务通过客户管理的密钥进行静态数据加密 | Microsoft Docs
description: 通过设备预配服务的客户管理的密钥对静态数据进行加密
author: wesmc7777
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: wesmc
ms.openlocfilehash: 1cc8afadb61d1684b16893d0eff23af227828eb1
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727076"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>通过设备预配服务的客户管理的密钥对静态数据进行加密

## <a name="overview"></a>概述

设备预配服务 (DPS) 支持使用客户管理的密钥 (CMK) 对静态数据加密，该密钥也称为创建自己的密钥 (BYOK)。 DPS 对写入数据中心的静态数据和传输中的数据进行加密，并在你访问数据时解密。 默认情况下，DPS 使用 Microsoft 托管密钥来加密静态数据。 使用 CMK，可以通过选择使用从 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理的 key-encryption-key 对静态数据加密，在默认平台加密之上再获得一层加密。 这使你能够灵活地创建、轮换、禁用和撤销密钥。 如果为 DPS 配置了 CMK，则意味着已启用双重加密，通过两层保护对数据进行积极地保护。 

此功能需要创建新的 DPS。 若要试用此功能，请通过 [Microsoft 支持](https://azure.microsoft.com/support/create-ticket/)人员与我们联系。 联系 Microsoft 支持人员时，请提供你的公司名称和订阅 ID。


## <a name="next-steps"></a>后续步骤

* [详细了解设备预配服务](./index.yml)

* [详细了解 Azure 密钥保管库](../key-vault/general/overview.md)