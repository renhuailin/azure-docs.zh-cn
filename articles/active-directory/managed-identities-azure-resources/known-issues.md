---
title: 托管标识的已知问题 - Azure Active Directory
description: Azure 资源托管标识的已知问题。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: ''
ms.openlocfilehash: bc43eef81a7c7bc5d66f1144bf8fc2c2e148bc49
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397709"
---
# <a name="known-issues-with-managed-identities"></a>托管标识的已知问题

本文讨论有关托管标识的几个问题以及如何解决这些问题。 [常见问题](managed-identities-faq.md)一文中介绍了有关托管标识的常见问题。
## <a name="vm-fails-to-start-after-being-moved"></a>迁移后无法启动 VM 

如果从资源组或订阅中移动处于运行状态的 VM，它将在移动过程中继续运行。 不过，如果在迁移后停止并重启 VM，那么它将无法启动。 导致此问题发生的原因是，VM 未更新对 Azure 资源托管标识的标识的引用，仍然继续指向旧资源组中的标识。

**解决方法** 
 
在 VM 上触发更新，以便它可以获取正确的 Azure 资源托管标识的值。 可以更改 VM 属性，从而更新对 Azure 资源托管标识的标识的引用。 例如，可以运行下列命令，在 VM 上设置新的标记值：

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
此命令在 VM 上设置值为 1 的新标记“fixVM”。 
 
通过设置此属性，VM 可以更新包含正确的 Azure 资源托管标识资源 URI，然后就应该能启动 VM 了。 
 
在 VM 启动后，便可以运行下列命令，从而删除此标记：

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>在 Azure AD 目录之间转移订阅

将订阅移动/转移到另一个目录时，托管标识不会更新。 因此，任何现存的系统分配的或用户分配的托管标识将被破坏。 

对于已移到另一目录的订阅中的托管标识，解决方法是：

 - 对于系统分配的托管标识：禁用并重新启用。 
 - 对于用户分配的托管标识：删除、重新创建并重新将其附加到所需的资源（例如虚拟机）

有关详细信息，请参阅[将 Azure 订阅转移到其他 Azure AD 目录](../../role-based-access-control/transfer-subscription.md)。


## <a name="next-steps"></a>后续步骤

你可以查看列出了[支持托管标识的服务](services-support-managed-identities.md)和[常见问题](managed-identities-faq.md)的文章
