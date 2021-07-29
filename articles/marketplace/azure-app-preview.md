---
title: 为 Azure 应用程序产品/服务添加预览版受众
description: 为合作伙伴中心（Azure 市场）的 Azure 应用程序产品/服务添加预览版受众。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 74f270922694dd0416638b8d429eca31cebf2609
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542294"
---
# <a name="add-a-preview-audience-for-an-azure-application-offer"></a>为 Azure 应用程序产品/服务添加预览版受众

本文介绍如何为商业市场中的 Azure 应用产品/服务配置预览版受众。 你需要定义预览版受众，他们可以在产品/服务上线前查看产品/服务列表。

## <a name="define-a-preview-audience"></a>定义预览版受众

在“预览版受众”页上，可以定义能够在上线前查看 Azure 应用产品/服务的有限受众，然后再面向更广泛的市场受众上线发布。 使用 Azure 订阅 ID 定义预览版受众，以及每个受众的可选说明。 客户无法查看所有这些字段。 在 Azure 门户的“订阅”页上，可找到 Azure 订阅 ID。

至少添加一个 Azure 订阅 ID，最多可添加 10个，可单独添加（最多 10 个），也可以通过上传 CSV 文件（最多 100 个）进行添加，由此定义在发布之前可以预览你的产品/服务的用户。 如果产品/服务已推出，则仍可以定义预览受众来测试产品/服务更改或是对产品/服务的更新。

> [!NOTE]
> 预览受众不同于专用受众。 预览受众可以在你的产品/服务于在线商店发布之前对其进行访问。 他们可以查看和验证所有计划，包括仅在将产品/服务完整发布到市场之后才向专用受众提供的计划。 可以建立仅适用于专用受众的计划。 专用受众（在计划“可用性”选项卡中定义）具有对特定计划的独占访问权限。

### <a name="add-subscription-ids-manually"></a>手动添加订阅 ID

1. 在“预览版受众”页上，在提供的框中添加一个 Azure 订阅 ID 和可选说明。
1. 若要添加另一个 ID，请选择“添加 ID（最多 10 个）”链接。
1. 选择“保存草稿”，然后继续下一个选项卡：技术配置。
1. 转到[下一步](#next-steps)。

### <a name="add-subscription-ids-with-a-csv-file"></a>使用 CSV 文件添加订阅 ID

1. 在“预览版受众”页面上，选择“导出受众 (csv)”链接。
1. 在 Microsoft Excel 等合适的应用程序中打开 .CSV 文件。
1. 在 .CSV 文件的“ID”列中，输入要添加到预览版受众的 Azure 订阅 ID。
1. 在“说明”列中，可以选择性地为每个电子邮件地址添加描述。
1. 对于在 B 列中输入的每个订阅 ID，请在 A 列中输入“SubscriptionID”的“类型”。
1. 另存为 .CSV 文件。
1. 在“预览版受众”页上，选择“导入受众 (csv)”链接。
1. 在“确认”对话框中，选择“是”。 
1. 选择此 .CSV 文件，然后选择“打开”。
1. 选择“保存草稿”，然后继续下一个选项卡：技术配置。

## <a name="next-steps"></a>后续步骤

- [向此产品/服务添加技术详细信息](azure-app-technical-configuration.md)
