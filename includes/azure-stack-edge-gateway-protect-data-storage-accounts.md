---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285185"
---
设备与用作 Azure 中数据的目标的存储帐户关联。 对存储帐户的访问权限由与该存储帐户关联的订阅以及 512 位存储访问密钥控制。

Azure Stack Edge 设备访问存储帐户时，将使用其中某个密钥进行身份验证。 保留其他密钥，以便可以定期轮换密钥。

出于安全原因，很多数据中心都要求密钥轮换。 我们建议遵循密钥轮换的以下最佳实践：

- 存储帐户密钥类似于存储帐户的根密码。 请小心保护帐户密钥。 请勿将密码分发给其他用户、对其进行硬编码或将其以纯文本格式保存在其他人可以访问的位置。
- 如果你认为帐户密钥可能已泄漏，请通过 Azure 门户[重新生成帐户密钥](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys)。
- Azure 管理员应该定期使用 Azure 门户的“存储”部分来直接访问存储帐户，从而更改或重新生成主密钥或辅助密钥。
- 还可以使用自己的加密密钥来保护 Azure 存储帐户中的数据。 指定客户托管密钥时，该密钥用于保护和控制对数据加密密钥的访问。 有关如何保护数据的详细信息，请参阅[为 Azure 存储帐户启用客户管理的密钥](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account)。
