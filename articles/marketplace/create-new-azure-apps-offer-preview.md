---
title: 如何为 Azure 应用程序产品/服务添加预览版观众
description: 了解如何在合作伙伴中心为 Azure 应用程序产品/服务添加预览版。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369999"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>如何为 Azure 应用程序产品/服务添加预览版观众

本文介绍如何为商业应用商店中的 Azure 应用程序产品/服务配置预览受众。 你需要定义预览观众，他们可以在活动之前查看你的产品/服务列表。

## <a name="define-a-preview-audience"></a>定义预览受众

在 " **预览观众** " 页上，你可以定义可在将其发布到更广泛的 marketplace 受众之前查看 Azure 应用程序提议的有限受众。 使用 Azure 订阅 Id 定义预览受众，并定义每个用户的可选说明。 客户无法查看所有这些字段。 在 Azure 门户的“订阅”页上，可找到 Azure 订阅 ID。

最少添加一个最多10个 Azure 订阅 Id，每个订阅 Id 分别 (最多10个) 或上传 CSV 文件 (最多 100) ，以定义在发布 live 之前可以预览你的产品/服务的用户。 如果产品/服务已推出，则仍可以定义预览受众来测试产品/服务更改或是对产品/服务的更新。

> [!NOTE]
> 预览受众不同于专用受众。 预览版用户在在线商店中发布之前，允许其访问你的产品/服务。 他们可以查看和验证所有计划，包括仅在将产品/服务完整发布到市场之后才向专用受众提供的计划。 你可以将计划仅提供给专用用户。  (在计划的 " **可用性** " 选项卡中定义的专用受众) 具有特定计划的独占访问权限。

### <a name="add-email-addresses-manually"></a>手动添加电子邮件地址

1. 在 " **预览受众** " 页上，在提供的框中添加单个 AZURE 订阅 ID 和可选描述。
1. 若要添加其他电子邮件地址，请选择 " **添加 ID (最大 10)** " 链接。
1. 选择 " **保存草稿** "，然后继续下一个选项卡：技术配置。
1. 请参阅 [后续步骤](#next-steps)。

### <a name="add-email-addresses-using-the-csv-file"></a>使用 CSV 文件添加电子邮件地址

1. 在 " **预览观众** " 页上，选择 " **导出访问群体 (csv)** " 链接。
1. 打开。应用程序（例如 Microsoft Excel）中的 CSV 文件。
1. 在。"CSV 文件" 的 " **ID** " 列中，输入要添加到预览受众的 Azure 订阅 id。
1. 在 " **说明** " 列中，可以选择性地为每个电子邮件地址添加描述。
1. 在 " **类型** " 列中，将 " **SubscriptionID** " 添加到每个具有电子邮件地址的行。
1. 将该文件另存为。CSV 文件。
1. 在 " **预览受众** " 页上，选择 " **导入访问群体 (csv)** " 链接。
1. 在 " **确认** " 对话框中，选择 **"是"** 。
1. 选择。CSV 文件，然后选择 " **打开** "。
1. 选择 " **保存草稿** "，然后继续下一个选项卡：技术配置。

## <a name="next-steps"></a>后续步骤

- [如何为 Azure 应用程序产品/服务添加技术详细信息](create-new-azure-apps-offer-technical.md)
