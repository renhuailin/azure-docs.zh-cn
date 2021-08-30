---
title: 使用 Azure AD Connect 云同步中的表达式生成器
description: 本文介绍如何使用云同步中的表达式生成器。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef8a039b61a0dd787c65ec66a3acb48a09bc1b2
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506576"
---
# <a name="expression-builder-with-cloud-sync"></a>云同步中的表达式生成器
表达式生成器是 Azure 中的一个新功能，在云同步下面可以找到它。它可以帮助你生成复杂的表达式。 在将这些表达式应用于云同步环境之前，可以使用表达式生成器对其进行测试。

## <a name="use-the-expression-builder"></a>使用表达式生成器
若要访问表达式生成器，请执行以下操作：

 1. 在 Azure 门户中，选择“Azure Active Directory”。 
 1. 选择“Azure AD Connect”。
 1. 选择“管理云同步”。
 1. 在“配置”下选择你的配置。
 1. 在“管理特性”下，选择“单击以编辑映射” 。
 1. 在“编辑特性映射”窗格中，选择“添加特性映射” 。
 1. 在“映射类型”下，选择“表达式” 。
 1. 选择“试用表达式生成器(预览版)”。
 
    ![该屏幕截图显示如何使用表达式生成器。](media/how-to-expression-builder/expression-1.png)

## <a name="build-an-expression"></a>生成表达式
在本部分，你将使用下拉列表从支持的函数中进行选择。 然后根据所选的函数填写其他框。 选择“应用表达式”后，语法将显示在“表达式输入”框中 。

例如，从下拉列表中选择“Replace”后，会提供更多的框。 该函数的语法显示在淡蓝色的框中。 显示的框对应于所选函数的语法。 Replace 的工作方式取决于提供的参数。

对于此示例，在提供 oldValue 和 replacementValue 后，源中出现的所有 oldValue 都将替换为 replacementValue   。

有关详细信息，请参阅 [Replace](reference-expressions.md#replace)。

要做的第一件事是选择作为 replace 函数的源的特性。 在此示例中已选择 mail 特性。

接下来，找到 oldValue 对应的框并输入 @fabrikam.com 。 最后，在 replacementValue 对应的框中，填写值 @contoso.com 。

简单而言，我们的表达式会将具有 @fabrikam.com 值的用户对象中的 mail 特性替换为 @contoso.com 值。 选择“添加表达式”时，可以在“表达式输入”框中看到语法 。

>[!NOTE]
>请务必根据选择“Replace”时出现的语法，将值放入与 oldValue 和 replacementValue 对应的框中  。

有关支持的表达式的详细信息，请参阅[在 Azure Active Directory 中编写特性映射的表达式](reference-expressions.md)。

### <a name="information-on-expression-builder-input-boxes"></a>有关表达式生成器输入框的信息
根据所选的函数，表达式生成器提供的框接受多个值。 例如，JOIN 函数接受字符串或者与给定特性关联的值。 例如，可以使用 [givenName] 特性值中包含的值，并将此值与 @contoso.com 字符串值相联接，以创建电子邮件地址 。

  ![该屏幕截图显示输入框值。](media/how-to-expression-builder/expression-8.png)

有关可接受的值以及如何编写表达式的详细信息，请参阅[在 Azure Active Directory 中编写特性映射的表达式](reference-expressions.md)。

## <a name="test-an-expression"></a>测试表达式
在此部分可以测试表达式。 从下拉列表中选择“mail”特性。 填写 @fabrikam.com 作为值，然后选择“测试表达式” 。

值 @contoso.com 将显示在“查看表达式输出”框中 。

 ![该屏幕截图显示如何测试表达式。](media/how-to-expression-builder/expression-4.png)

## <a name="deploy-the-expression"></a>部署表达式
如果对该表达式感到满意，请选择“应用表达式”。

![该屏幕截图显示如何添加表达式。](media/how-to-expression-builder/expression-5.png)

此操作会将该表达式添加到代理配置。

![该屏幕截图显示代理配置。](media/how-to-expression-builder/expression-6.png)

## <a name="set-a-null-value-on-an-expression"></a>在表达式中设置 NULL 值
若要将特性的值设置为 NULL，请使用包含 `""` 值的表达式。 此表达式会将 NULL 值传递到目标特性。

![该屏幕截图显示 NULL 值。](media/how-to-expression-builder/expression-7.png)


## <a name="next-steps"></a>后续步骤 

- [在 Azure Active Directory 中编写特性映射的表达式](reference-expressions.md)
- [云同步配置](how-to-configure.md)
