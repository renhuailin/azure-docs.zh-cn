---
title: 在 Microsoft AppSource 中设置 Azure 容器产品/服务的预览版受众。
description: 在 Microsoft AppSource 中设置 Azure 容器产品/服务的预览版受众。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 0d8bd02e247a3db0429f227e396abbfbc7cdfd91
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892324"
---
# <a name="set-the-preview-audience-for-an-azure-container-offer"></a>设置 Azure 容器产品/服务的预览版受众

本文介绍如何使用合作伙伴中心为商业市场中的 Azure 容器产品/服务配置预览版受众。 预览版受众可以在产品/服务上线前查看它们。

## <a name="define-a-preview-audience"></a>定义预览版受众

在“预览版受众”选项卡上，可以先定义能够查看容器产品/服务的有限受众，然后再将其发布给更广泛的市场受众。 使用 Azure 订阅 ID 定义预览版受众，以及每个受众的可选说明。 客户无法查看所有这些字段。 可以在 Azure 门户的“订阅”页面上找到 Azure 订阅 ID。

至少添加一个 Azure 订阅 ID，可单独添加（最多 10 个）或通过上传 CSV 文件（最多 100 个）添加，以定义可以预览你的产品/服务的用户。 如果产品/服务已上线，你仍然可以定义预览版受众，以测试对产品/服务的任何更新。

## <a name="add-email-addresses-manually"></a>手动添加电子邮件地址

1. 在“预览版受众”页面上，在提供的框中添加一个 Azure 订阅 ID 和一段可选说明。
1. 要添加其他电子邮件地址，请选择“添加 ID（最多 10 个）”链接。
1. 选择“保存草稿”，然后转到下一选项卡设置计划。

## <a name="add-email-addresses-using-a-csv-file"></a>使用 CSV 文件添加电子邮件地址

1. 在“预览版受众”页面上，选择“导出受众 (csv)”链接。
1. 打开 CSV 文件。 在“ID”列中，输入要添加到预览版受众的 Azure 订阅 ID。
1. 在“描述”列中，可以选择为每个条目添加描述。
1. 在“类型”列中，将“SubscriptionId”添加到含有 ID 的每一行。
1. 将文件另存为 CSV 文件。
1. 在“预览版受众”页面上，选择“导入受众 (csv)”链接。
1. 在“确认”对话框中，选择“是”，然后上传 CSV 文件。
1. 选择“保存草稿”，然后转到下一选项卡设置计划。

> [!IMPORTANT]
> 在查看处于预览状态的产品/服务后，必须选择“上线”向公众发布产品/服务。

## <a name="next-steps"></a>后续步骤

- [创建和管理计划](azure-container-plan-overview.md)
