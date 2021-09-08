---
title: Azure API 管理中的修订 | Microsoft Docs
description: 了解 Azure API 管理中的修订的概念。
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new, devx-track-azurepowershell
ms.openlocfilehash: 16a5dc58a8fddca2cef2c393aaecca61fef61a3c
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187714"
---
# <a name="revisions-in-azure-api-management"></a>Azure API 管理中的修订

使用修订，能够通过可控且安全的方式对 API 进行更改。 若想进行更改，请创建新的修订。 然后，可以在不干扰 API 使用者的情况下编辑和测试 API。 准备就绪后，即可将修订设为最新。 同时，你可以根据需要将条目发布到更改日志中，以使 API 使用者了解最新的更改。 将更改日志发布到开发人员门户。

> [!NOTE]
> 在“消耗”层中，开发人员门户不可用。

使用修订，你可以：

- 在不干扰生产 API 的情况下，安全地更改 API 定义和策略。
- 在发布更改之前尝试更改。
- 记录所做的更改，以便开发人员可以了解新内容。
- 如果发现问题，请回滚。

[按照我们的演练开始使用修订。](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>访问特定的修订版

可以使用特殊格式的 URL 访问 API 的每个修订版。 在 API URL 的末尾，但在查询字符串之前追加 `;rev={revisionNumber}`，以访问该 API 的特定修订版。 例如，可以使用此 URL 来访问 `customers` API 的修订版 3：

`https://apis.contoso.com/customers;rev=3?customerId=123`

默认情况下，每个修订版具有与当前修订版相同的安全设置。 如果要为每个修订版应用不同的安全性，则可以有意更改特定修订版的策略。 例如，你可能想要添加 [IP 筛选策略](./api-management-access-restriction-policies.md#RestrictCallerIPs)，以防止外部调用方访问仍处于开发阶段的修订版。

修订版可以脱机使用，这使得调用方无法进行访问，即使试图通过其 URL 访问修订版也是如此。 你可以使用 Azure 门户将修订版标记为脱机。 如果使用 PowerShell，则可使用 `Set-AzApiManagementApiRevision` cmdlet，并将 `Path` 参数设置为 `$null`。

> [!NOTE]
> 建议在不使用修订版进行测试时将其脱机。

## <a name="current-revision"></a>当前修订版

单个修订版可设置为当前修订版。 此修订版将用于所有未在 URL 中指定显式修订号的 API 请求。 你可以通过将该修订版设置为“当前版本”来回滚到先前的修订版。

可以使用 Azure 门户将修订版标记为当前版本。 如果使用 PowerShell，则可以使用 `New-AzApiManagementApiRelease` cmdlet。

## <a name="revision-descriptions"></a>修订版说明

创建修订版时，可以为自己的跟踪目的设置说明。 API 用户无法播放说明。

将修订版设置为“当前版本”时，还可以选择指定公共更改日志说明。 更改日志包含在开发人员门户中，供 API 用户查看。 你可以使用 `Update-AzApiManagementApiRelease` PowerShell cmdlet 修改更改日志说明。

> [!NOTE]
> 某些 API 属性（例如“显示名称”和“API 后缀”）只能在当前修订版中更新 。

## <a name="versions-and-revisions"></a>版本和修订版

版本和修订版功能不同。 与非版本控制 API 一样，每个版本都可以有多个修订版。 你可以使用修订版而不使用版本，或反之亦然。 通常，版本用于分离 API 版本和重大更改，而修订版可用于对 API 进行次要和非重大的更改。

如果你发现修订版有重大更改，或者想要将其正式转换为 beta /测试版本，则可以从修订版中创建一个版本。 使用 Azure 门户，单击“修订版”选项卡上的修订版上下文菜单中的“从修订版创建版本”。
