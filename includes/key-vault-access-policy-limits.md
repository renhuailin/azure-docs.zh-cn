---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 20ef9a4f30aafee562096e584c4b80fef236b062
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "108749711"
---
Key Vault 最多支持 1024 个访问策略条目，每个条目可向特定安全主体授予一组不同的权限。 由于此限制，建议你尽可能将访问策略分配给用户组，而不是单个用户。 使用组来管理组织中多个人员的权限要轻松得多。 有关详细信息，请参阅[使用 Azure Active Directory 组管理应用和资源访问](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

有关 Key Vault 访问控制的完整详细信息，请参阅 [Azure Key Vault 安全性功能：标识和访问管理](../articles/key-vault/general/security-features.md#identity-management)。