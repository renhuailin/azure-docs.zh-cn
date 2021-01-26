---
title: Azure AD Connect 云同步意外删除
description: 本主题介绍如何使用意外删除功能来防止删除。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785159"
---
# <a name="accidental-delete-prevention"></a>意外删除防护

以下文档介绍 Azure AD Connect 云同步的意外删除功能。 意外删除功能旨在防止发生意外的配置更改，以及对可能会影响许多用户和组的本地目录的更改。  可以使用此功能实现以下操作：

- 配置阻止自动删除的功能。 
- 将 (阈值) 的对象数设置为超过配置将生效的值 
- 设置通知电子邮件地址，以便在此方案中将相关同步作业放入隔离状态后，他们可以收到电子邮件通知 

若要使用此功能，请设置对象数的阈值（如果已删除，则同步应停止）。  因此，如果达到此数量，同步将停止，并且通知将发送到指定的电子邮件。  此通知将允许你调查正在进行的操作。


## <a name="configure-accidental-delete-prevention"></a>配置意外删除保护
若要使用新功能，请执行以下步骤。


1.  在 Azure 门户中，选择“Azure Active Directory”。 
2.  选择“Azure AD Connect”。
3.  选择 " **管理云同步**"。
4. 在 " **配置**" 下，选择您的配置。
5. 在 " **设置** " 下填写以下内容：
    - **通知电子邮件** -用于通知的电子邮件
    - **防止意外删除** -选中此复选框以启用该功能
    - **意外删除阈值** -输入要停止同步的对象数并发送通知

![意外删除](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>从意外删除实例恢复
如果遇到意外删除，你会在预配代理配置的状态上看到此情况。  它将显示 " **删除阈值已超过**"。
 
![意外删除状态](media/how-to-accidental-deletes/delete-1.png)

单击 " **超出删除阈值**" 即可查看同步状态信息。  这将提供其他详细信息。 
 
 ![同步状态](media/how-to-accidental-deletes/delete-2.png)

通过右键单击省略号，你将获得以下选项：
 - 查看设置日志
 - 查看代理
 - 允许删除

 ![右键单击](media/how-to-accidental-deletes/delete-3.png)

使用 " **查看设置日志**"，可以查看 **StagedDelete** 条目，并查看在已删除的用户上提供的信息。
 
 ![“预配”日志](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>允许删除

" **允许删除** " 操作将删除触发意外删除阈值的对象。  使用以下过程接受删除。  

1. 右键单击省略号，然后选择 " **允许删除**"。
2. 在确认上单击 **"是** " 以允许删除。
 
 ![确认时为是](media/how-to-accidental-deletes/delete-4.png)

3. 你将看到确认删除已被接受并且状态将返回到下一个周期的状态。 
 
 ![接受删除](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>拒绝删除

如果你不希望允许删除，则需要执行以下操作：
- 调查删除的源
- 解决该问题 (例如，OU 已移出意外范围，你现在已将其重新添加回范围) 
- 对代理配置运行 **重新启动同步**

## <a name="next-steps"></a>后续步骤 

- [Azure AD Connect 云同步故障排除？](how-to-troubleshoot.md)
- [Azure AD Connect 云同步错误代码](reference-error-codes.md)
 

