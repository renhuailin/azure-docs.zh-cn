---
title: 选择退出 Azure Active Directory 可验证凭据（预览版）
description: 了解如何选择退出可验证凭据预览
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 1fe2d9755eac9a4048f7ed61388d0cdc7914552c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466088"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>选择退出可验证凭据（预览版）

本文内容：

- 需要选择退出的原因。
- 所需步骤。
- 你的数据会发生什么？
- 对现有可验证凭据的影响。

> [!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

- 加入完整的可验证凭据。

## <a name="potential-reasons-for-opting-out"></a>选择退出的可能原因

目前，我们无法对域信息进行修改。 因此，如果你有出错或决定要进行更改，则除了选择退出并再次开始以外，没有其他选项。

## <a name="the-steps-required"></a>所需步骤

1. 从 Azure 门户搜索可验证凭据。
2. 从左侧菜单中选择“设置”。
3. 在“重置组织”部分下，选择“删除所有凭据，并选择退出预览”。 

   ![设置重置组织](media/how-to-opt-out/settings-reset.png)

4. 阅读警告消息，然后继续选择“删除并选择退出”。

   ![设置删除并退出](media/how-to-opt-out/delete-and-opt-out.png)

你现在已选择退出可验证凭据预览。 请继续阅读以了解后台发生的情况。

## <a name="what-happens-to-your-data"></a>你的数据会发生什么？

完成选择退出 Azure Active Directory 可验证凭据服务后，将进行以下操作：

- Key Vault 中的 DID 键是否被[软删除](../../key-vault/general/soft-delete-overview.md)。
- 将从数据库中删除凭证颁发者对象。
- 租户标识符将从数据库中删除。 
- 将从数据库中删除所有协定对象。

选择退出后，你将无法恢复操作或对 DID 执行任何操作。 此步骤是单向操作，你需要重新选择加入，这将导致新的 DID 被创建。  

## <a name="effect-on-existing-verifiable-credentials"></a>对现有可验证凭据的影响

已颁发的所有可验证凭据将继续存在。 它们不会被加密，你的 DID 将仍然可通过离子来解析。
但是，当信赖方调用状态 API 时，它们总是会接收到失败消息。

## <a name="next-steps"></a>后续步骤

- 在你的 [Azure 租户](get-started-verifiable-credentials.md)上设置可验证凭据