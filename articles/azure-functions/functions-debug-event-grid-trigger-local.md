---
title: Azure 函数事件网格本地调试
description: 了解如何在本地调试事件网格事件触发的 Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 240b721d71ef53ad6f43f076f1d5db0e29b556a4
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970918"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Azure 函数事件网格触发器本地调试

本文演示如何调试一个用于处理存储帐户所引发 Azure 事件网格事件的本地函数。 

## <a name="prerequisites"></a>先决条件

- 创建或使用现有的函数应用
- 创建或使用现有的存储帐户。 可以在 `BlobStorage`、`StorageV2` 或 [Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 的 Azure 存储帐户上设置事件网格通知订阅。
- 下载 [ngrok](https://ngrok.com/)，使 Azure 能够调用本地函数

## <a name="create-a-new-function"></a>创建新函数

在 Visual Studio 中打开函数应用，在解决方案资源管理器中右键单击项目名称，然后单击“添加”>“新建 Azure 函数”。

在“新建 Azure 函数”窗口中选择“事件网格触发器”，然后单击“确定”。 

![创建新的函数](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

创建函数后，打开代码文件，并复制文件顶部已注释掉的 URL。 配置事件网格触发器时将使用此位置。

![复制位置](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

然后，在以 `log.LogInformation` 开头的行中设置一个断点。

![设置断点](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


接下来，**按 F5** 启动调试会话。

[!INCLUDE [functions-event-grid-local-dev](../../includes/functions-event-grid-local-dev.md)]

## <a name="debug-the-function"></a>调试函数

事件网格识别到已将新文件上传到存储容器之后，即会命中本地函数中的断点。

![启动 ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>清理资源

若要清理本文中创建的资源，请删除存储帐户中的 **test** 容器。

## <a name="next-steps"></a>后续步骤

- [使用事件网格自动调整已上传图像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Functions 的事件网格触发器](./functions-bindings-event-grid.md)