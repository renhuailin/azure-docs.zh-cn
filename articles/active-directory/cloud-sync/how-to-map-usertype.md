---
title: 通过 Azure AD Connect 云同步映射 UserType
description: 本文介绍如何通过云同步映射 UserType 属性。
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
ms.openlocfilehash: 7b06381472549fd7d9b3f3b5dcd222fcd96f4f15
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506198"
---
# <a name="map-usertype-with-cloud-sync"></a>通过云同步映射 UserType

云同步支持同步用户对象的 UserType 属性。

默认情况下，UserType 属性未启用同步，因为在本地 Active Directory 中没有相应的 UserType 属性 。 必须手动添加此映射以进行同步。 在执行此步骤之前，必须注意 Azure Active Directory (Azure AD) 强制实施的以下行为：

- Azure AD 只接受 UserType 属性的两个值：Member 和 Guest。
- 如果没有在云同步中映射 UserType 属性，则通过目录同步创建的 Azure AD 用户的 UserType 属性将设置为“Member” 。

在为 UserType 属性添加映射之前，必须首先确定如何从本地 Active Directory 派生属性。 以下方法最常见：

 - 指定一个未使用的本地 Active Directory 属性（如 extensionAttribute1）作为源属性。 指定的本地 Active Directory 属性应为字符串类型、单值且包含值“Member”或“Guest”。
 - 如果选择此方法，则在启用 UserType 属性同步之前，必须确保为本地 Active Directory 中同步到 Azure AD 的所有现有用户对象使用适当的值填充指定的属性。

## <a name="add-the-usertype-mapping"></a>添加 UserType 映射
若要添加 UserType 映射，请执行以下操作：

 1. 在 Azure 门户中，选择“Azure Active Directory”。 
 1. 选择“Azure AD Connect”。
 1. 选择“管理云同步”。
 1. 在“配置”下选择你的配置。
 1. 在“管理特性”下，选择“单击以编辑映射” 。
 
    ![此屏幕截图显示了如何编辑属性映射。](media/how-to-map-usertype/usertype-1.png) 

 1. 选择“添加属性映射”。
 
    ![此屏幕截图显示了如何添加新的属性映射。](media/how-to-map-usertype/usertype-2.png) 
1. 选择映射类型。 可以通过以下三种方式之一执行映射：
   - 直接映射，例如，从 Active Directory 属性进行的直接映射
   - 一个表达式，例如 IIF(InStr([userPrincipalName], "@partners") > 0,"Guest","Member")
   - 一个常量，例如，将所有用户对象设为“Guest”
 
     ![此屏幕截图显示了如何添加 UserType 属性。](media/how-to-map-usertype/usertype-3.png)

1. 在“目标属性”下拉框中，选择“UserType” 。
1. 选择页面底部的“应用”，为 Azure AD 的 UserType 属性创建映射。

## <a name="next-steps"></a>后续步骤 

- [在 Azure Active Directory 中编写特性映射的表达式](reference-expressions.md)
- [云同步配置](how-to-configure.md)
