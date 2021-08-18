---
title: Azure 门户中的故障排除 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure 门户中的“故障排除”页监视 IoT Edge 设备和模块
author: kgremban
ms.author: kgremban
ms.date: 05/26/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e0761262463d69fe80f7dc15ed1a5944dcc235bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726534"
---
# <a name="troubleshoot-iot-edge-devices-from-the-azure-portal"></a>在 Azure 门户中对 IoT Edge 设备进行故障排除

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge 提供了一种在 Azure 门户中针对模块的监视和故障排除的简化方法。 “故障排除”页是 IoT Edge 代理直接方法的包装器，使你可以轻松地从部署的模块中检索日志，并以远程方式重启它们。

## <a name="prerequisites"></a>先决条件

如果你使用长期支持分支，门户中此故障排除功能的全部功能需要 IoT Edge 1.1.3 或更高版本；如果你使用最新的稳定分支，则需要 1.2.1 或更高版本。 如果使用 IoT Edge 主机组件和 edgeAgent 模块，需要安装这些版本。

## <a name="access-the-troubleshooting-page"></a>访问“故障排除”页

可以通过“IoT Edge 设备详细信息”页或“IoT Edge 模块详细信息”页访问门户中的“故障排除”页。

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。

1. 在左窗格的菜单中，选择“IoT Edge”  。

1. 从设备列表中选择要监视的 IoT Edge 设备。

1. 在“设备详细信息”页中，可以从菜单中选择“故障排除”或选择要检查的特定模块的运行时状态。

   ![在“设备详细信息”页中选择“故障排除”或模块运行时状态](./media/troubleshoot-in-portal/troubleshoot-from-device-details.png)

1. 在“设备详细信息”页中，还可以选择模块的名称来打开“模块详细信息”页。 在该页中，可以从菜单中选择“故障排除”。

   ![在“模块详细信息”页中选择“故障排除”](./media/troubleshoot-in-portal/troubleshoot-from-module-details.png)

## <a name="view-module-logs-in-the-portal"></a>在门户中查看模块日志

在“故障排除”页上，可以查看和下载 IoT Edge 设备上任何正在运行的模块中的日志。

此页的最大日志行数限制为 1500 行，超过此限制的任何日志都将被截断。 如果日志过大，获取模块日志的尝试将会失败。 在这种情况下，请尝试更改时间范围筛选器以检索较少的数据，或考虑使用直接方法[从 IoT Edge 部署中检索日志](how-to-retrieve-iot-edge-logs.md)以收集大型日志文件。

使用下拉菜单选择要检查的模块。

![从下拉菜单中选择模块](./media/troubleshoot-in-portal/select-module.png)

默认情况下，此页显示最近 15 分钟的日志。 选择“时间范围”筛选器以查看不同日志。 使用滑块选择过去 60 分钟内的时间范围，或选中“输入时间”以选择特定的日期时间范围。

![选择时间范围](./media/troubleshoot-in-portal/select-time-range.png)

在获取了要检查的时间范围内要进行故障排除的模块中的日志后，可以使用“查找”筛选器从日志中检索特定的行。 可以筛选警告或错误，也可以提供要搜索的特定字词或短语。 对于较复杂的搜索，“查找”功能支持纯文本搜索或 [.NET 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。

可以将模块日志以文本文件形式下载。 下载的日志文件将反映已应用于日志的所有活动筛选器。

>[!TIP]
>设备上的 CPU 利用率将暂时达到峰值，因为它会收集日志以响应来自门户的请求。 此行为符合预期，任务完成后，利用率应该会稳定下来。

## <a name="restart-modules"></a>重启模块

“故障排除”页包含重启模块的功能。 选择此选项会向 IoT Edge 代理发送命令以重启所选模块。 重启模块不会影响你在重启之前检索日志的能力。

![从“故障排除”页重启模块](./media/troubleshoot-in-portal/restart-module.png)

## <a name="next-steps"></a>后续步骤

查找有关[对 IoT Edge 设备进行故障排除](troubleshoot.md)的更多提示，或了解[常见问题和解决方法](troubleshoot-common-errors.md)。 

如果你还有其他问题，请创建[支持请求](https://portal.azure.com/#create/Microsoft.Support)以获取帮助。
