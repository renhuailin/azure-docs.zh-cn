---
title: 在 Azure 市场中为 SaaS 产品/服务添加预览版受众
description: 在 Azure 市场中为软件即服务 (SaaS) 产品/服务添加预览版观众。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: fe42b5c7d9953a3e8bc15c4dbd351d3d2beb4d33
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110614398"
---
# <a name="add-a-preview-audience-for-a-saas-offer"></a>为 SaaS 产品/服务添加预览版受众

在合作伙伴中心创建软件即服务 (SaaS) 产品/服务时，你需要定义预览版受众，这些受众可以在产品/服务上线之前查看你的产品/服务列表。 本文介绍了如何配置预览版受众。

> [!NOTE]
> 如果选择单独处理事务，则不会看到此选项， 而是会跳到[如何销售 SaaS 产品/服务](create-new-saas-offer-marketing.md)。

## <a name="define-a-preview-audience"></a>定义预览版受众

在“预览版受众”选项卡上，你可以定义可查看 SaaS 产品/服务的受限受众，然后将其发布到更广泛的市场受众。 你可以将邀请发送到 Microsoft 帐户 (MSA) 或 Azure Active Directory (Azure AD) 电子邮件地址。 手动添加最少一个且最多 10 个电子邮件地址，或使用 .csv 文件最多导入 20 个电子邮件地址。 可以随时更新预览版受众列表。

> [!NOTE]
> 预览受众不同于专用受众。 预览版受众可以在你的产品/服务在在线商店中上线之前对其进行访问。 你也可以选择创建一个计划并仅面向专用受众提供。 [如何为 SaaS 产品/服务创建计划](create-new-saas-offer-plans.md)中介绍了私有计划。

### <a name="add-email-addresses-manually"></a>手动添加电子邮件地址

1. 在“预览版受众”页上，在提供的框中添加一个 Azure AD 或 MSA 电子邮件地址和可选说明。
1. 要添加其他电子邮件地址，请选择“添加其他电子邮件”链接。
1. 选择“保存草稿”，然后继续下一个选项卡：**定义技术配置**。
1. 继续查看[如何为 SaaS 产品/服务添加技术详细信息](create-new-saas-offer-technical.md)。

### <a name="add-email-addresses-using-the-csv-file"></a>使用 CSV 文件添加电子邮件地址

1. 在“预览版受众”页面上，选择“导出受众 (csv)”链接。
1. 在 Microsoft Excel 等应用程序中打开 .CSV 文件。
1. 在 .CSV 文件的“ID”列中，输入要添加到预览版受众的电子邮件地址。
1. 在“说明”列中，可以选择性地为每个电子邮件地址添加描述。
1. 在“类型”列中，将 MixedAadMsaId 添加到具有电子邮件地址的每一行。
1. 将文件另存为 .CSV 文件。
1. 在“预览版受众”页面上，选择“导入受众 (csv)”链接。
1. 在“确认”对话框中，选择“是”。 
1. 选择 CSV 文件，然后选择“打开”。
1. 选择“保存草稿”，然后继续下一个选项卡：技术配置。

## <a name="next-steps"></a>后续步骤

- [为 SaaS 产品/服务添加技术详细信息](create-new-saas-offer-technical.md)
