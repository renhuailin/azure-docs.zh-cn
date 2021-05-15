---
title: 如何在 Microsoft 合作伙伴中心配置咨询服务的定价和可用性
description: 了解如何在使用合作伙伴中心的 Microsoft 商业市场中配置咨询服务产品/服务的定价详细信息和市场可用性。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 6b386238bd759714bc0c8ad81d67c29aa1774aba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780223"
---
# <a name="how-to-configure-your-consulting-service-pricing-and-availability"></a>如何配置咨询服务的定价和可用性

本文介绍如何在 Microsoft 商业市场中定义咨询服务产品/服务的市场可用性和定价详细信息。

> [!NOTE]
> 咨询服务产品/服务的报价仅供显示。 任何交易都必须在你和你的客户在商业市场外部进行管理。

## <a name="markets"></a>市场

在“市场”部分中，选择将提供咨询服务的国家或地区。

1. 在“市场”下，选择“编辑市场”链接。 
2. 在出现的对话框中，选择要在其中提供产品/服务的市场位置。 必须至少选择 1 个市场，最多可以选择 141 个市场。
3. 选择“保存”以关闭该对话框。

## <a name="preview-audience"></a>预览版受众

发布或更新咨询服务产品/服务时，合作伙伴中心会创建该列表的预览版本。 此预览版仅对具有“隐藏密钥”的用户可见。

在“隐藏密钥”字段中，输入一个字母数字字符串，你将使用该字符串来访问产品/服务的预览版本。

## <a name="pricing-informational-only"></a>定价（仅供参考）

在“定价”部分中，定义产品/服务是免费还是付费。

对于付费是免费，请指定价格是固定的还是估计的。 如果估计价格，你的是免费描述中必须描述将影响价格的因素。

如果您在“市场”部分中选择了一个国家或地区，请以该市场支持的货币提供价格，然后选择“保存草稿”。  有关受支持货币的列表，请参阅[商业市场的地理可用性和货币支持](./marketplace-geo-availability-currencies.md)。

如果您在“市场”部分中选择了多个国家或地区，请提供美元（USD）的价格，然后选择“保存草稿”。  合作伙伴中心使用保存草稿时可用的汇率将此价格转换为所有所选市场的本地货币。

要验证转换或在单个市场中设置自定义价格，需要导出、修改随后导入定价电子表格：

1. 在“定价”下面，选择“导出定价数据”链接。  这会将文件下载到你的设备。
1. 在 Microsoft Excel 中打开 exportedPrice.xlsx 文件。
1. 在电子表格中，可以调整每个市场的价格和货币。 有关受支持货币的列表，请参阅[商业市场的地理可用性和货币支持](./marketplace-geo-availability-currencies.md)。 完成后，保存文件。
1. 在合作伙伴中心的“定价”下，选择“导入定价数据”链接。  导入文件将覆盖以前的定价信息。

> [!IMPORTANT]
> 在合作伙伴中心定义的价格是静态的，不遵循汇率中的变化。 要在发布后更改一个或多个市场中的价格，请在合作伙伴中心更新并重新提交你的产品/服务。

选择“保存草稿”，然后继续操作。

## <a name="next-steps"></a>后续步骤

* [审阅和发布](review-publish-offer.md)