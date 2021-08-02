---
title: 了解如何在 Azure Active Directory 中配合使用表达式生成器与应用程序预配
description: 了解如何在 Azure Active Directory 中配合使用表达式生成器与应用程序预配。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 06/02/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 336005a94d4403d6a9e3fb125001b78a68179aa4
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962078"
---
# <a name="understand-how-expression-builder-in-application-provisioning-works"></a>了解应用程序预配中表达式生成器的工作原理

可以使用[表达式](functions-for-customizing-application-data.md)来[映射属性](./customize-application-attributes.md)。 以前，必须手动创建这些表达式并输入表达式框。 表达式生成器一种可用于创建表达式的工具。

:::image type="content" source="media/expression-builder/expression-builder.png" alt-text="选择函数前的默认表达式生成器页面。" lightbox="media/expression-builder/expression-builder.png":::

有关生成表达式的参考，请参阅[有关为属性映射编写表达式的参考](functions-for-customizing-application-data.md)。 

## <a name="finding-the-expression-builder"></a>查找表达式生成器

在应用程序预配中，可以使用属性映射的表达式。 可以选择“显示高级选项”来访问属性映射页面上的表达式生成器，然后选择“表达式生成器” 。

:::image type="content" source="media/expression-builder/accessing-expression-builder.png" alt-text="已选中显示高级设置的复选框，并显示了一个指示表达式生成器的链接" lightbox="media/expression-builder/accessing-expression-builder.png":::

## <a name="using-expression-builder"></a>使用表达式生成器

若要使用表达式生成器，请选择一个函数和属性，然后根据需要输入后缀。 然后，选择“添加表达式”将表达式添加到代码框。 若要详细了解可用的函数及其使用方法，请参阅[有关为属性映射编写表达式的参考](functions-for-customizing-application-data.md)。

搜索用户或输入值，然后选择“测试表达式”来测试表达式。 表达式测试的输出将在“查看表达式输出”框中显示。

如果对表达式满意，请将其移至属性映射。 将其复制并粘贴到正在使用的属性映射的表达式框。

## <a name="known-limitations"></a>已知的限制
* 无法在表达式生成器中选择扩展属性。 但可以在属性映射表达式中使用扩展属性。 

## <a name="next-steps"></a>后续步骤

[有关为属性映射编写表达式的参考](functions-for-customizing-application-data.md)