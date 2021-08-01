---
title: 如何使用 Azure AD Connect 云同步中的表达式生成器
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
ms.openlocfilehash: e169c2cbabf9e9e37bb93b1e3ee26080105d501e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776447"
---
# <a name="expression-builder-with-cloud-sync"></a>云同步中的表达式生成器
表达式生成器是 Azure 中的一个新边栏选项卡，在云同步下面可以找到它。它可以帮助生成复杂的表达式，并可让你在将这些表达式应用于云同步环境之前先对其进行测试。

## <a name="use-the-expression-builder"></a>使用表达式生成器
若要访问表达式生成器，请使用以下步骤。

 1. 在 Azure 门户中，选择“Azure Active Directory”
 2. 选择“Azure AD Connect”。
 3. 选择“管理云同步”。
 4. 在“配置”下选择你的配置。
 5. 在“管理特性”下，选择“单击以编辑映射” 。
 6. 在“编辑特性映射”边栏选项卡上，单击“添加特性映射” 。
 7. 在“映射类型”下，选择“表达式” 。
 8. 选择“试用表达式生成器(预览版)”。
 ![使用表达式生成器](media/how-to-expression-builder/expression-1.png)

## <a name="build-an-expression"></a>生成表达式
此部分允许使用下拉列表从支持的函数列表中进行选择。  然后，它会根据所选的函数提供附加字段供你填写。  选择“应用表达式”后，语法将显示在“表达式输入”框中 。

例如，从下拉列表中选择“Replace”后，会显示更多的框。  该函数的语法显示在淡蓝色的框中。  显示的框对应于所选函数的语法。  Replace 的工作方式取决于提供的参数。  在本示例中，我们将使用：

- 当提供了 oldValue 和 replacementValue 时：
    - 将 source 中出现的所有 oldValue 替换为 replacementValue

有关详细信息，请参阅 [Replace](reference-expressions.md#replace)

我们首先要做的就是选择作为 replace 函数的源的特性。 在本示例中，我们选择了 mail 特性。 

接下来，填充 oldValue 的值。  此 oldValue 将是 @fabrikam.com。  最后，在 replacementValue 的框中填充值 @contoso.com。

因此，简单而言，我们的表达式会将用户对象上具有 @fabrikam.com 值的 mail 特性替换为 @contoso.com 值。  单击“添加表达式”按钮可以在“表达式输入”中看到语法 


>[!NOTE]
>请务必根据选择“Replace”时出现的语法，将值填放入与 oldValue 和 replacementValue 对应的框中。

有关支持的表达式的详细信息，请参阅[在 Azure Active Directory 中编写特性映射的表达式](reference-expressions.md)

### <a name="information-on-expression-builder-input-boxes"></a>有关表达式生成器输入框的信息
根据所选的函数，表达式生成器提供的框接受多个值。  例如，JOIN 函数接受字符串或者与给定特性关联的值。  例如，可以使用 [givenName] 特性值中包含的值，并将此值与“@contoso.com”字符串值相联接，以创建电子邮件地址。

  ![输入框值](media/how-to-expression-builder/expression-8.png)

有关可接受的值以及如何编写表达式的详细信息，请参阅[在 Azure Active Directory 中编写特性映射的表达式](reference-expressions.md)。

## <a name="test-an-expression"></a>测试表达式
在此部分可以测试表达式。  从下拉列表中选择“mail”特性。  在“值”中填入 @fabrikam.com，然后单击“测试表达式” 。  

@contoso.com 值将显示在“查看表达式输出”框中 。

 ![测试表达式](media/how-to-expression-builder/expression-4.png)

## <a name="deploy-the-expression"></a>部署表达式
对表达式感到满意后，单击“应用表达式”按钮即可。
![添加表达式](media/how-to-expression-builder/expression-5.png)

这会将表达式添加到代理配置。
![代理配置](media/how-to-expression-builder/expression-6.png)

## <a name="setting-a-null-value-on-an-expression"></a>在表达式中设置 NULL 值
将特性值设置为 NULL。  可以使用包含 `""` 值的表达式。  这会将 NULL 值传递到目标特性。

![NULL 值](media/how-to-expression-builder/expression-7.png)



## <a name="next-steps"></a>后续步骤 

- [在 Azure Active Directory 中编写特性映射的表达式](reference-expressions.md)
- [云同步配置](how-to-configure.md)
