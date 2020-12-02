---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466381"
---
设备与用作 Azure 中数据的目标的存储帐户关联。 对存储帐户的访问权限由与该存储帐户关联的订阅以及 512 位存储访问密钥控制。

当 Data Box Edge 设备访问存储帐户时，将使用其中一个密钥进行身份验证。 保留其他密钥，以便可以定期轮换密钥。

出于安全原因，很多数据中心都要求密钥轮换。 我们建议遵循密钥轮换的以下最佳实践：

- 存储帐户密钥类似于存储帐户的根密码。 请小心保护帐户密钥。 请勿将密码分发给其他用户、对其进行硬编码或将其以纯文本格式保存在其他人可以访问的位置。
- 如果你认为帐户密钥可能已泄漏，请通过 Azure 门户[重新生成帐户密钥](../articles/storage/common/storage-account-manage.md#regenerate-access-keys)。
- Azure 管理员应定期更改或重新生成主密钥或辅助密钥，方法是使用 Azure 门户的 "存储" 部分直接访问存储帐户。