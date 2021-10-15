---
title: 客户管理的密钥 - Azure 视频分析器
description: 可将客户管理的密钥（即自带密钥）与 Azure 视频分析器配合使用。
author: anilmur
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: e86d2eb8883efb887a598f93d191ae9070ef2b67
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402982"
---
# <a name="customer-managed-keys-with-azure-video-analyzer"></a>客户管理的密钥与 Azure 视频分析器

自带密钥 (BYOK) 是一种 Azure 范围的计划，可帮助客户将其工作负载转移到云中。 客户管理的密钥使客户能够遵守行业合规性法规并改进服务的租户隔离。 通过让客户控制加密密钥，可以在 Microsoft 服务中最大程度地减少不必要的访问和控制并建立信心。

## <a name="keys-and-key-management"></a>密钥和密钥管理

系统会为所有视频分析器帐户创建一个帐户密钥。 默认情况下，该帐户密钥使用视频分析器拥有的系统密钥（即系统管理的密钥）进行加密。 你也可以改为将自己的密钥用于 Azure 视频分析器。 在这种情况下，帐户密钥使用你自己的密钥进行加密。 访问策略和视频资源元数据将使用帐户密钥进行加密。

视频分析器使用用户分配的托管标识从你拥有的密钥保管库读取密钥。 创建或更新视频分析器帐户时，必须提供用户分配的托管标识，并为密钥保管库分配适当的 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)。 视频分析器要求密钥保管库与帐户位于同一个区域，并且已启用软删除和清除保护。

密钥可以是 2048、3072 或 4096 RSA 密钥，同时支持 HSM 和软件密钥。

> [!NOTE]
> 不支持 EC 密钥。

可以指定密钥名称和密钥版本，或仅指定密钥名称。 当你仅使用密钥名称时，视频分析器将使用最新的密钥版本。 系统将自动检测新版本的客户密钥，并重新对帐户密钥进行加密。

> [!WARNING]
> 视频分析器会监视对客户密钥的访问。 如果客户密钥变为不可访问（例如，已删除密钥、密钥保管库或访问授权），则视频分析器会将该帐户转换为客户密钥不可访问状态，从而有效地禁用帐户。 可以删除该帐户，也可以还原帐户密钥来恢复访问。

## <a name="double-encryption"></a>双重加密

视频分析器根据 Azure 标准做法使用双重加密来保护敏感数据，请参阅 [Azure 双重加密](../../security/fundamentals/double-encryption.md)。 对于静态数据，第一层加密使用客户管理的密钥或 Microsoft 托管密钥，具体取决于帐户的 `encryption` 设置。 使用单独的 Microsoft 托管密钥自动提供静态数据加密的第二层。

> [!NOTE]
> 双重加密在视频分析器帐户上自动启用。 但需在存储帐户上分别配置客户管理的密钥和双重加密。 参阅[存储加密](../../storage/common/storage-service-encryption.md)。


## <a name="next-steps"></a>后续步骤

参阅[托管标识](managed-identity.md)
