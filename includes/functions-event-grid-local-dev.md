---
title: include 文件
description: include 文件
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f2dcb92d0a600c57d96348413704863285cea3f0
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987177"
---
## <a name="allow-azure-to-call-your-local-function"></a>使 Azure 能够调用本地函数

若要在计算机上调试的函数处中断，必须通过某种方式使 Azure 能够在云中与本地函数通信。

[ngrok](https://ngrok.com/) 实用工具可让 Azure 调用计算机上运行的函数。 使用以下命令启动 *ngrok*：

```bash
ngrok http -host-header=localhost 7071
```
设置该实用工具后，命令窗口应如以下屏幕截图所示：

![屏幕截图显示了启动“ngrok”实用程序后的命令提示符。](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-ngrok.png)

复制运行 *ngrok* 时生成的 **HTTPS** URL。 配置事件网格事件终结点时将使用此值。

## <a name="add-a-storage-event"></a>添加存储事件

1. 打开 Azure 门户，导航到存储帐户，然后从左侧菜单中选择“事件”选项。

    ![添加存储帐户事件](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-add-event.png)

1. 在“事件”窗口中，选择“事件订阅”按钮。 

1. 在“事件订阅”窗口中，选择“终结点类型”下拉列表并选择“Webhook” 。

    ![选择订阅类型](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-type.png)

1. 配置终结点类型后，单击“选择终结点”以配置终结点值。

    ![选择终结点类型](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-endpoint.png)

    “订阅者终结点”值由三个不同的值构成。 前缀是 *ngrok* 生成的 HTTPS URL。 URL 的其余部分来自之前在方法指南中复制的 localhost URL，末尾添加了函数名称。 先处理 localhost URL：将 `http://localhost:7071` 替换为 ngrok URL，将 `{functionname}` 替换为函数名称。

1. 下面的屏幕截图显示了使用 `Event Grid` 触发器类型时最终 URL 应如何显示的示例。

    ![终结点选择](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-endpoint-selection.png)

1. 输入适当的值后，单击“确认选择”。

> [!IMPORTANT]
> 每次启动 *ngrok* 时，都会重新生成 HTTPS URL，并且值会更改。 因此，每次通过 *ngrok* 向 Azure 公开函数时，都必须创建新的事件订阅。

## <a name="upload-a-file"></a>上传文件

现在，可将一个文件上传到存储帐户，以触发由本地函数处理的事件网格事件。 

打开[存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)并将其连接到存储帐户。 

- 展开“Blob 容器” 
- 单击右键并选择“创建 Blob 容器”。
- 将容器命名为“示例-工作项”
- 选择“示例-工作项”容器
- 单击“上传”按钮
- 单击“上传文件”
- 选择文件并将其上传到 Blob 容器

