---
title: 测试 UI 定义文件
description: 介绍如何测试通过门户创建 Azure 托管应用程序的用户体验。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: tomfitz
ms.openlocfilehash: 55a966c17f3bcd51f354198e36e03071efd4834d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951473"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>测试 Azure 托管应用程序的门户接口

为托管应用程序[创建 createUiDefinition.json 文件](create-uidefinition-overview.md)之后，需要测试用户体验。 为了简化测试，请使用一个可在门户中加载文件的沙盒环境。 不需要真正部署该托管应用程序。 沙盒在当前的全屏门户体验中提供用户界面。 建议使用沙盒来预览接口。

## <a name="prerequisites"></a>必备条件

* 一个 **createUiDefinition.json** 文件。 如果没有此文件，请复制[示例文件](https://github.com/Azure/azure-quickstart-templates/blob/master/demos/100-marketplace-sample/createUiDefinition.json)。

* Azure 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="use-sandbox"></a>使用沙盒

1. 打开[创建 UI 定义沙箱](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)。

   ![显示沙盒](./media/test-createuidefinition/show-sandbox.png)

1. 将空定义替换为 createUiDefinition.json 文件的内容。 选择“预览”  。

   ![选择“预览”](./media/test-createuidefinition/select-preview.png)

1. 此时会显示已创建的窗体。 可以单步执行用户体验，并填写值。

   ![显示窗体](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>故障排除

如果在选择“预览”  后窗体未显示，则可能存在语法错误。 在右侧滚动条上查找红色指示器，然后导航到它。

![显示语法错误](./media/test-createuidefinition/show-syntax-error.png)

如果窗体未显示，但看到一个带泪滴的云图标，则表明窗体出现错误，例如缺少属性。 在浏览器中打开“Web 开发人员工具”。 “控制台”显示有关界面的重要消息。 

![显示错误](./media/test-createuidefinition/show-error.png)

## <a name="test-your-solution-files"></a>测试解决方案文件

确认门户界面按预期方式工作后，可以验证 createUiDefinition 文件是否能够正常地与 mainTemplate.json 文件相集成。 可以运行验证脚本测试来测试解决方案文件的内容，包括 createUiDefinition 文件。 该脚本会验证 JSON 语法，检查文本字段中的正则表达式，并确保门户界面的输出值与模板参数相匹配。 有关运行此脚本的信息，请参阅[对模板运行静态验证检查](https://aka.ms/arm-ttk)。

## <a name="next-steps"></a>后续步骤

验证门户界面之后，请了解如何[在市场中提供 Azure 托管应用程序](../../marketplace/azure-app-offer-setup.md)。