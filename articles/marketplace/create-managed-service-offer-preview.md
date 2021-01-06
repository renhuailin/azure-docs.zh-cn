---
title: 如何为托管服务产品/服务添加预览版观众
description: 了解如何在 Microsoft 合作伙伴中心为托管服务产品/服务添加预览版。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918106"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>如何为托管服务产品/服务添加预览版观众

本文介绍如何使用合作伙伴中心为商业 marketplace 中的托管服务产品配置预览受众。 预览观众可以在现场之前查看你的产品/服务。

## <a name="define-a-preview-audience"></a>定义预览受众

在 " **预览观众** " 页上，你可以定义在将其发布到更广泛的 marketplace 群体之前，可查看托管服务产品/服务的受限受众。 使用 Azure 订阅 Id 定义预览受众，并定义每个用户的可选说明。 客户无法查看所有这些字段。 你可以在 " **订阅** " 页上的 "Azure 门户" 中找到你的 AZURE 订阅 ID。

至少添加一个 Azure 订阅 ID，将其单独 (最多10个) 或上传 CSV 文件 (最多 100) ，以定义在发布 live 之前可以预览你的产品/服务的用户。 如果产品/服务已处于活动阶段，则仍可定义用于测试产品/服务更新的预览版观众。

> [!NOTE]
> *预览* 受众不同于 *专用* 用户。 预览版用户在在线商店中发布之前，允许其访问你的产品/服务。 他们可以查看和验证所有计划，包括那些在 marketplace 中的产品/服务已完全发布后仅对专用用户可用的计划。 你可以将计划仅提供给专用用户。  (在计划的 "可用性" 选项卡中定义的专用受众) 具有特定计划的独占访问权限。

## <a name="add-email-addresses-manually"></a>手动添加电子邮件地址

1. 在 " **预览受众** " 页上，在提供的框中添加单个 AZURE 订阅 ID 和可选描述。
2. 若要添加其他电子邮件地址，请选择 " **添加 ID (最大 10)** " 链接。
3. 选择 " **保存草稿** "，然后继续下一个选项卡。

## <a name="add-email-addresses-using-a-csv-file"></a>使用 CSV 文件添加电子邮件地址

1. 在 "预览受众 **预览观众** " 页上，选择 " **导出受众 (csv)** " 链接。
2. 打开 CSV 文件。 在 " **Id** " 列中，输入要添加到预览受众的 Azure 订阅 id。
3. 在 " **说明** " 列中，可以选择为每个条目添加描述。
4. 在 " **类型** " 列中，将 " **SubscriptionId** " 添加到具有 ID 的每一行。
5. 将该文件另存为 CSV 文件。
6. 在 " **预览受众** " 页上，选择 " **导入访问群体 (csv)** " 链接。
7. 在 " **确认** " 对话框中，选择 **"是**"，然后上传 CSV 文件。
8. 选择 " **保存草稿** "，然后继续下一个选项卡。

## <a name="next-steps"></a>后续步骤

* [创建计划](create-managed-service-offer-plans.md)
