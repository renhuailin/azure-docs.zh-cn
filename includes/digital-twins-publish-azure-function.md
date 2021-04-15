---
author: baanders
description: 用于从 Visual Studio 发布 Azure 函数的进程的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 9f8ca9ab6dc184abb8d9dcd25ebe87cbd7761722
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630246"
---
若要在 Azure 中将项目发布到函数应用，请在解决方案资源管理器中右键选择该项目，然后选择“发布”。

> [!IMPORTANT] 
> 在 Azure 中发布到函数应用将对订阅产生额外的费用，这与 Azure 数字孪生无关。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="显示右键选择解决方案菜单的 Visual Studio 屏幕截图。菜单中突出显示了“发布”。":::

在接下来的“发布”页中，保留选择的默认 Azure 目标，然后选择“下一步”。 

对于特定目标，请选择“Azure 函数应用(Windows)”，然后选择“下一步”。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="“发布 Azure 函数”对话中的 Visual Studio 屏幕截图。“特定目标”页面上选中了“Azure 函数应用(Windows)”。":::

在“Functions 实例”页上，选择你的订阅。 然后，选择 *+* 图标以创建新的 Azure 函数。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="“发布 Azure 函数”对话中的 Visual Studio 屏幕截图。“函数实例”页上突出显示了用于创建新函数的“+”按钮。":::

在“函数应用(Windows) - 新建”窗口中，按如下所示填写字段：
* “名称”是 Azure 将用于托管 Azure Functions 应用的消耗计划的名称。 这也将成为保存实际函数的函数应用的名称。 你可以选择自己的唯一值，也可以保留默认建议。
* 确保“订阅”与要使用的订阅匹配 
* 确保“资源组”是你想要使用的资源组
* 将“计划类型”保留为“消耗”
* 在“位置”中，选择符合资源组位置的位置
* 使用“新建…”链接创建新的 Azure 存储资源。 设置与资源组相匹配的位置，使用其他默认值，然后选择“确定”。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="“发布 Azure 函数”对话中的 Visual Studio 屏幕截图。正在填写新函数应用的详细信息，包括名称、订阅、资源组、计划类型、位置和 Azure 存储。":::

然后选择“创建”。

在创建应用服务时，请稍等片刻，然后对话框应返回到“Functions 实例”页，新的函数应用将出现在资源组下方嵌套的“函数应用”区域中。 选择“完成”。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="在 Visual Studio 中发布 Azure 函数：Functions 实例（在函数应用之后）":::

在 Visual Studio 主窗口重新打开的“发布”窗格中，检查所有信息是否都正确无误，然后选择“发布”。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="“发布 Azure 函数”对话中的 Visual Studio 屏幕截图。新函数应用显示在函数应用列表中，有一个“完成”按钮。":::

> [!NOTE]
> 如果看到如下所示的弹出窗口：:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="名为“发布凭据”的 Visual Studio 弹出窗口的屏幕截图。它包含用户名和密码的字段，以及用于尝试从 Azure 检索凭据的按钮。" border="false":::
> 依次选择“尝试从 Azure 检索凭据”和“保存” 。
>
> 若看到“升级 Azure 上的 Functions 版本”或“你的 Functions 运行时版本与在 Azure 中运行的版本不匹配”警告 ：
>
> 请按照提示升级到最新的 Azure Functions 运行时版本。 如果你在使用较旧版本的 Visual Studio，则可能会发生此问题。

函数应用现在将发布到 Azure。