---
title: Azure AD Connect 云同步意外删除
description: 本主题介绍如何使用意外删除功能来防止删除。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5055b847eab1ee53688abfa308b5969f7526f668
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742679"
---
# <a name="accidental-delete-prevention"></a>防止意外删除

以下文档介绍 Azure AD Connect 云同步的意外删除功能。意外删除功能旨在防止发生意外的配置更改，以及防止发生影响许多用户和组的本地目录更改。  可以使用此功能实现以下操作：

- 配置阻止自动意外删除的功能。 
- 设置对象数上限（阈值），超过该值配置将会生效 
- 设置通知电子邮件地址，以便此方案中相关同步作业被置于隔离状态后，用户可以收到电子邮件通知 

要使用此功能，请设置对象数的阈值（如果被删除，同步应停止）。  这样，如果达到此数量，同步将停止，并会向指定的电子邮件发送通知。  通过此通知可以调查发送的状况。

有关其他信息和示例，请参阅以下视频。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mV]


## <a name="configure-accidental-delete-prevention"></a>配置防止意外删除
要使用该新功能，请按照以下步骤执行操作。


1.  在 Azure 门户中，选择“Azure Active Directory”。 
2.  选择“Azure AD Connect”。
3.  选择“管理云同步”。
4. 在“配置”下选择你的配置。
5. 在“设置”下，填写以下内容：
    - 通知电子邮件 - 用于通知的电子邮件
    - 防止意外删除 - 选中此复选框可启用该功能
    - 意外删除阈值 - 输入将停止同步并发送通知的对象数

![意外删除](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>从意外删除实例中恢复
如果遇到意外删除，则会通过预配代理配置的状态看到此情况。  它将显示“已超过删除阈值”。
 
![意外删除状态](media/how-to-accidental-deletes/delete-1.png)

点击“已超过删除阈值”，可查看同步状态信息。  这样将提供更多详细信息。 
 
 ![同步状态](media/how-to-accidental-deletes/delete-2.png)

通过右键单击省略号，可获得以下选项：
 - 查看预配日志
 - 查看代理
 - 允许删除

 ![右键单击](media/how-to-accidental-deletes/delete-3.png)

使用“查看预配日志”，可以查看 StagedDelete 条目，并可查看对已删除的用户提供的信息。
 
 ![“预配”日志](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>允许删除

“允许删除”操作将删除触发意外删除阈值的对象。  请按照以下过程接受删除。  

1. 右键单击省略号，然后选择“允许删除”。
2. 对于确认单击“是”以允许删除。
 
 ![确认上的“是”](media/how-to-accidental-deletes/delete-4.png)

3. 你将看到关于已接受删除的确认，并且状态将返回到下一周期的正常状态。 
 
 ![接受删除](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>拒绝删除

如果不想允许删除，则需要执行以下操作：
- 调查删除原因
- 解决问题（例如，OU 被意外移出范围，现在已将其重新添加到范围之内）
- 对代理配置运行“重新启动同步”

## <a name="next-steps"></a>后续步骤 

- [Azure AD Connect 云同步故障排除？](how-to-troubleshoot.md)
- [Azure AD Connect 云同步错误代码](reference-error-codes.md)
 

