---
title: Azure 通信服务 - 启用和访问呼叫摘要与呼叫诊断日志
titleSuffix: An Azure Communication Services concept document
description: 如何访问 Azure Monitor 中的呼叫摘要与呼叫诊断日志
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 07/22/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 9d047746c29fb932fcb9ea7aeb2738606453b158
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744703"
---
# <a name="enable-and-access-call-summary-and-call-diagnostic-logs"></a>启用和访问呼叫摘要与呼叫诊断日志

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

若要访问 Azure 通信服务语音和视频资源的遥测数据，请执行以下步骤。

## <a name="enable-logging"></a>启用日志记录
1. 首先，需要为日志创建一个存储帐户。 请参阅[创建存储帐户](../../storage/common/storage-account-create.md?tabs=azure-portal)，了解有关如何完成此步骤的说明。 另请参阅[存储帐户概述](../../storage/common/storage-account-overview.md)，详细了解不同存储选项的类型和功能。 如果你已有 Azure 存储帐户，请转到步骤 2。
 
1. 创建存储帐户后，接下来需要按照[在资源中启用诊断日志](./logging-and-diagnostics.md#enable-diagnostic-logs-in-your-resource)中的说明启用日志记录。 你将选中日志“CallSummaryPRIVATEPREVIEW”和“CallDiagnosticPRIVATEPREVIEW”对应的复选框。 

1. 接下来，选中“存档到存储帐户”框，然后在下面的下拉菜单中选择日志的存储帐户。 “发送到 Analytics 工作区”选项目前不适用于此功能的个人预览版，但在此功能公开发布后，该选项将可用。

:::image type="content" source="media\call-logs-images\call-logs-access-diagnostic-setting.png" alt-text="Azure Monitor 诊断设置":::



## <a name="access-your-logs"></a>访问日志

若要访问日志，请通过在 Azure 门户中导航到[存储帐户](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)，转到上述步骤 3 中指定的存储帐户。 

:::image type="content" source="media\call-logs-images\call-logs-access-storage.png" alt-text="Azure 门户存储":::

在那里，可以下载所有日志或个别日志。

:::image type="content" source="media\call-logs-images\call-logs-access-storage-resource.png" alt-text="Azure 门户存储下载":::

## <a name="next-steps"></a>后续步骤

- 详细了解[日志记录和诊断](./logging-and-diagnostics.md)