---
title: 如何通过 Azure AD Connect 云同步映射 UserType
description: 本文介绍如何通过云同步映射 UserType。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14bbeb7339b3af41b24b25aed2332ac8367e99ae
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776436"
---
# <a name="map-usertype-with-cloud-sync"></a>通过云同步映射 UserType

云同步可为用户对象的 UserType 属性同步提供支持。 

默认情况下，UserType 属性未启用同步，因为在本地 Active Directory 中没有相应的 UserType 属性。 必须手动添加此映射以进行同步。 执行此操作之前，必须注意 Azure AD 强制实施的以下行为：

- Azure AD 只接受 UserType 属性的两个值：Member 和 Guest。
- 如果没有在云同步中映射 UserType 属性，则通过目录同步创建的 Azure AD 用户的 UserType 属性将设置为 Member。

在为 UserType 属性添加映射之前，必须首先确定如何从本地 Active Directory 派生属性。 下面是最常见的方法：

 - 指定要用作源属性的尚未使用的本地 AD 属性（例如 extensionAttribute1）。 指定的本地 AD 属性应为字符串类型，具有单值，且包含值 Member 或 Guest。
 - 如果选择此方法，在启用 UserType 属性同步之前，必须确保指定的属性中填充了本地 Active Directory 中已同步到 Azure AD 的所有现有用户对象的正确值。

## <a name="to-add-the-usertype-mapping"></a>添加 UserType 映射
若要添加 UserType 映射，请采取以下步骤。

 1. 在 Azure 门户中，选择“Azure Active Directory”
 2. 选择“Azure AD Connect”。
 3. 选择“管理云同步”。
 4. 在“配置”下选择你的配置。
 5. 在“管理属性”下，选择“单击以编辑映射” 。
  ![编辑属性映射](media/how-to-map-usertype/usertype-1.png) 

 6. 单击“添加属性映射”。
    ![添加新的属性映射](media/how-to-map-usertype/usertype-2.png) 
7. 选择映射类型。 可以通过以下三种方式之一执行映射：
 - 直接映射（即来自 AD 属性）
 - 表达式 (IIF(InStr([userPrincipalName], "@partners") > 0,"Guest","Member"))
 - 常数（即将所有用户对象作为来宾）。
  ![添加 usertype](media/how-to-map-usertype/usertype-3.png) 
8. 在“目标属性”下拉列表中，选择“UserType”。
9. 单击页面底部的“应用”按钮。 该操作会为 Azure AD UserType 属性创建映射。

## <a name="next-steps"></a>后续步骤 

- [在 Azure Active Directory 中编写属性映射的表达式](reference-expressions.md)
- [云同步配置](how-to-configure.md)
