---
title: 为托管服务产品/服务添加预览版受众
description: 在 Azure 市场中为托管服务产品/服务添加预览版受众。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 7/16/2021
ms.openlocfilehash: 38fbf04f9e8f961cca99bd3e0b23e05e664f4848
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649706"
---
# <a name="add-a-preview-audience-for-a-managed-service-offer"></a>为托管服务产品/服务添加预览版受众

本文介绍如何使用合作伙伴中心为商业市场中的托管服务产品/服务配置预览版受众。 预览版受众可以在你的产品/服务上线之前查看它们。

## <a name="define-a-preview-audience"></a>定义预览版受众

在“预览版受众”页上，你可以定义可查看托管服务产品/服务的受限受众，然后将其发布到更广泛的市场受众。 使用 Azure 订阅 ID 指定预览版受众，以及每个受众的可选说明。 客户无法查看所有这些字段。 可以在 Azure 门户的“订阅”页上找到 Azure 订阅 ID。

至少添加一个 Azure 订阅 ID，可单独添加（最多 10 个）或通过上传 CSV 文件（最多 100 个）添加，以定义在发布之前可以预览你的产品/服务的用户。 如果产品/服务已上线，仍然可以定义预览版受众，以测试对产品/服务的任何更新。

> [!NOTE]
> “预览版”受众不同于“专用”受众。 预览版受众可以在你的产品/服务在在线商店中上线之前对其进行访问。 他们可以查看和验证所有计划，包括仅在将产品/服务完整发布到市场之后才向专用受众提供的计划。 你可以将计划仅提供给专用受众。 专用受众（在计划“可用性”选项卡中定义）具有对特定计划的独占访问权限。

## <a name="add-email-addresses-manually"></a>手动添加电子邮件地址

1. 在“预览版受众”页面上，在提供的框中添加一个 Azure 订阅 ID 和一段可选说明。
2. 要添加其他电子邮件地址，请选择“添加 ID（最多 10 个）”链接。
3. 选择“保存草稿”，然后转到下一选项卡。

## <a name="add-email-addresses-using-a-csv-file"></a>使用 CSV 文件添加电子邮件地址

1. 在“预览版受众”页面上，选择“导出受众 (csv)”链接。
2. 打开 CSV 文件。 在“ID”列中，输入要添加到预览版受众的 Azure 订阅 ID。
3. 在“描述”列中，可以选择为每个条目添加描述。
4. 在“类型”列中，将“SubscriptionId”添加到含有 ID 的每一行。
5. 将文件另存为 CSV 文件。
6. 在“预览版受众”页面上，选择“导入受众 (csv)”链接。
7. 在“确认”对话框中，选择“是”，然后上传 CSV 文件。

选择“保存草稿”，然后转到下一选项卡“计划概述”。

## <a name="next-steps"></a>后续步骤

* [创建计划](create-managed-service-offer-plans.md)
