---
title: Azure AD Connect 云同步中的特性映射
description: 本文介绍如何使用 Azure AD Connect 的云同步功能来映射特性。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555204"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect 云同步中的特性映射

可以使用 Azure Active Directory (Azure AD) Connect 的云同步功能，在本地用户或组对象与 Azure AD 中的对象之间映射特性。 此功能已添加到云同步配置。

可以根据业务需求自定义（更改、删除或创建）默认的特性映射。 有关同步的特性列表，请参阅[同步到 Azure Active Directory 的特性](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)。

## <a name="understand-types-of-attribute-mapping"></a>了解特性映射的类型
使用特性映射可以控制如何在 Azure AD 中填充特性。 Azure AD 支持四种映射类型：

- **直接**：使用 Active Directory 中链接对象的特性值填充目标特性。
- **常量**：使用指定的特定字符串填充目标特性。
- **表达式**：基于脚本式表达式的结果填充目标特性。 有关详细信息，请参阅[在 Azure Active Directory 中编写特性映射的表达式](reference-expressions.md)。
- **无**：目标特性保持不变。 不过，如果目标特性曾经为空，则会使用指定的默认值填充目标特性。

除这些基本类型以外，自定义特性映射还支持可选的默认值赋值概念。 默认值赋值确保当 Azure AD 或目标对象没有值时，使用某个值来填充目标特性。 最常见的配置是将此项留空。

## <a name="understand-properties-of-attribute-mapping"></a>了解特性映射的属性

除类型属性以外，特性映射还支持以下特性：

- **源特性**：来自源系统（例如 Active Directory）的用户特性。
- **目标特性**：目标系统（例如 Azure Active Directory）中的用户特性。
- **为 null 时的默认值(可选)** ：源特性为 null 时要传递给目标系统的值。 仅当已创建某个用户时，才预配此值。 更新现有用户时不会预配此值。  
- **应用此映射**：
  - **始终**：对用户创建和更新操作均应用此映射。
  - **仅创建期间**：仅对用户创建操作应用此映射。

> [!NOTE]
> 本文将介绍如何使用 Azure 门户来映射特性。  有关使用 Microsoft Graph 的信息，请参阅[转换](how-to-transformation.md)。

## <a name="add-an-attribute-mapping"></a>添加特性映射

若要使用新功能，请执行以下步骤：

1.  在 Azure 门户中，选择“Azure Active Directory”。 
2.  选择“Azure AD Connect”。
3.  选择“管理云同步”。

    ![屏幕截图，显示用于管理云同步的链接。](media/how-to-install/install-6.png)

4. 在“配置”下选择你的配置。
5. 选择“单击以编辑映射”。  单击此链接会打开“特性映射”屏幕。

    ![显示用于添加特性的链接的屏幕截图。](media/how-to-attribute-mapping/mapping-6.png)

6.  选择“添加属性”。

    ![显示用于添加特性的按钮以及特性和映射类型列表的屏幕截图。](media/how-to-attribute-mapping/mapping-1.png)

7. 选择映射类型。 对于本示例，我们使用“表达式”。
8. 在框中输入表达式。 对于本示例，我们使用 `Replace([mail], "@contoso.com", , ,"", ,)`。
9. 输入目标特性。 对于本示例，我们使用 **ExtensionAttribute15**。
10. 选择何时应用此映射，然后选择“应用”。

    ![显示用于创建特性映射的填充框的屏幕截图。](media/how-to-attribute-mapping/mapping-2a.png)

11. 返回到“特性映射”屏幕，应会看到新的特性映射。  
12. 选择“保存架构”。

    ![显示“保存架构”按钮的屏幕截图。](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>测试特性映射

若要测试特性映射，可以使用[按需预配](how-to-on-demand-provision.md)： 

1. 在 Azure 门户中，选择“Azure Active Directory”。 
2. 选择“Azure AD Connect”。
3. 选择“管理预配”。
4. 在“配置”下选择你的配置。
5. 在“验证”下选择“预配用户”按钮。  
6. 在“按需预配”屏幕上输入用户或组的可分辨名称，然后选择“预配”按钮。  

   屏幕将显示预配正在进行。

   ![显示预配正在进行的屏幕截图。](media/how-to-attribute-mapping/mapping-4.png)

8. 预配完成后，将显示包含四个绿色对勾标记的成功屏幕。 

   在“执行操作”下选择“查看详细信息”。  在右侧，应会看到已同步新特性并且已应用表达式。

   ![显示成功结果和导出详细信息的屏幕截图。](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)
- [为属性映射编写表达式](reference-expressions.md)
- [与 Azure Active Directory 同步的属性](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
