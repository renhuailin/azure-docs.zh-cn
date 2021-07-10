---
author: baanders
description: 用于从 Visual Studio 发布 Azure 函数的进程的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 49f57f39ba59575417f3336d395b1cfa6cf97eb6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110451067"
---
若要将项目发布到 Azure 中的函数应用，请在解决方案资源管理器中启动。 右键单击项目，然后选择“发布”。

> [!IMPORTANT] 
> 在 Azure 中发布到函数应用将对订阅产生额外的费用，这与 Azure 数字孪生无关。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="显示右键单击解决方案菜单的 Visual Studio 屏幕截图。菜单中突出显示了“发布”。":::

在打开的“发布”页上，保留选择的默认 Azure 目标 。 然后选择“下一步”。 

对于特定目标，请选择“Azure 函数应用(Windows)”，然后选择“下一步” 。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="显示“发布 Azure 函数”对话框的 Visual Studio 屏幕截图。在“特定目标”页上，选择“Azure 函数应用(Windows)”。":::

在“Functions 实例”选项卡上，选择你的订阅。 然后，选择加号 (+) 图标以创建新函数。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="显示“发布 Azure 函数”对话框的 Visual Studio 屏幕截图。其中突出显示了加号图标。":::

在“函数应用(Windows) - 新建”窗口中，填写以下字段：
* “名称”是 Azure 将用于托管 Azure Functions 应用的消耗计划的名称。 此名称也将应用于保存实际函数的函数应用。 你可以选择一个唯一值，也可以保留默认建议。
* 确保“订阅”与要使用的订阅匹配。 
* 确保“资源组”是你要使用的资源组
* 将“计划类型”选项保留为“消耗” 。
* 选择资源组的“位置”。
* 选择“新建”链接，创建新的 Azure 存储资源 。 设置与资源组相匹配的位置，使用其他默认值，然后选择“确定”。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Visual Studio 的屏幕截图，显示了“发布 Azure 函数”对话框页，其中正在填写新函数应用的详细信息。":::

然后选择“创建”。

创建应用服务后，将打开“Functions 实例”选项卡。 新的函数应用会显示在资源组下面的“函数应用”区域中。 选择“完成”。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Visual Studio 的屏幕截图，显示了已选择“Functions 实例”选项卡的“发布 Azure 函数”对话框。":::

在 Visual Studio 主窗口中打开的“发布”窗格中，检查所有信息是否都正确无误。 然后选择“发布”。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="显示“发布”窗格的 Visual Studio 屏幕截图。其中突出显示了“发布”按钮。":::

> [!NOTE]
> 如果看到类似于以下示例的弹出窗口，请选择“尝试从 Azure 检索凭据”，然后选择“保存” 。
> :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Visual Studio 的屏幕截图，显示了一个名为“发布凭据”的弹出窗口。" border="false":::
>
> 如果看到以下警告之一，请按照提示升级到最新的 Azure Functions 运行时版本：
> * “更新 Azure 上的 Functions 版本。”
> * “Functions 运行时版本与 Azure 中运行的版本不匹配。”
>
> 如果使用旧版 Visual Studio，则可能会出现这些警告。

函数应用现在将发布到 Azure。
