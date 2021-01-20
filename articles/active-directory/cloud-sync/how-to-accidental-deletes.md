---
title: Azure AD Connect 云同步意外删除
description: 本主题介绍如何使用意外删除功能来防止删除。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef7b6d9495b1431e03808b830671e839b90d436
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613180"
---
# <a name="accidental-delete-prevention"></a>意外删除防护

以下文档介绍 Azure AD Connect 云同步的意外删除功能。 意外删除功能旨在防止发生意外的配置更改，以及对可能会影响许多用户和组的本地目录的更改。  可以使用此功能实现以下操作：

- 配置阻止自动删除的功能。 
- 将 (阈值) 的对象数设置为超过配置将生效的值 
- 设置通知电子邮件地址，以便在此方案中将相关同步作业放入隔离后，他们可以收到电子邮件通知 

若要使用此功能，请设置对象数的阈值（如果已删除，则同步应停止）。  因此，如果达到此数量，同步将停止，并且通知将发送到指定的电子邮件。  这允许您调查正在进行的操作。


## <a name="configure-accidental-delete-prevention"></a>配置意外删除保护
若要使用新功能，请执行以下步骤。


1.  在 Azure 门户中，选择“Azure Active Directory”。 
2.  选择“Azure AD Connect”。
3.  选择 " **管理云同步**"。
4. 在 " **配置**" 下，选择您的配置。
5. 在 " **设置** " 下填写以下内容：
    - **通知电子邮件** -用于通知的电子邮件
    - **防止意外删除** -选中此复选框以启用该功能
    - **意外删除阈值** -输入一些对象来触发同步停止和通知

![意外删除](media/how-to-accidental-deletes/accident-1.png)

## <a name="next-steps"></a>后续步骤 

- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)
- [如何安装 Azure AD Connect 云同步](how-to-install.md)
 

