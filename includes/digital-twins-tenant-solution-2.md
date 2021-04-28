---
author: baanders
description: include 文件，用于描述用于解决 Azure 数字孪生的跨租户限制的代码解决方案
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 2702f3c70d9e6f1a0bdad8695414e2d5fab02411
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589312"
---
以下示例演示如何在 `DefaultAzureCredential` 选项中设置 `InteractiveBrowserTenantId` 的租户 ID 值：

:::image type="content" source="../articles/digital-twins/media/troubleshoot-error-404/defaultazurecredentialoptions.png" alt-text="代码屏幕截图，显示 DefaultAzureCredentialOptions 方法。其中 InteractiveBrowserTenantId 的值设置为示例租户 ID 值。":::

有类似的选项可用于为租户设置对 Visual Studio 和 Visual Studio Code 的身份验证。 有关可用选项的详细信息，请参阅 [DefaultAzureCredentialOptions 文档](/dotnet/api/azure.identity.defaultazurecredentialoptions?view=azure-dotnet&preserve-view=true)。