---
title: 适用于 DNS 的 Azure Defender - 优点和功能
description: 了解适用于 DNS 的 Azure Defender 的优点和功能
author: memildin
ms.author: memildin
ms.date: 05/12/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 976ffc86fd82bb85fefb16b4f442111a07d1f652
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003595"
---
# <a name="introduction-to-azure-defender-for-dns"></a>适用于 DNS 的 Azure Defender 简介

[Azure DNS](../dns/dns-overview.md) 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。

适用于 DNS 的 Azure Defender 通过以下方式为连接到 Azure DNS 的资源提供了额外的一层保护：

- 持续监视来自 Azure 资源的所有 DNS 查询
- 运行高级安全分析，以发出有关可疑活动的警报

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|用于 DNS 的 Azure Defender 按[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)中显示的定价计费|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/地区/主权（US Gov，Azure 中国）|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>适用于 DNS 的 Azure Defender 有哪些优点？

适用于 DNS 的 Azure Defender 会保护连接到 Azure DNS 的资源，防止出现以下问题：

- 使用 DNS 隧道从 Azure 资源中泄漏数据
- 恶意软件与 C&C 服务器通信
- 以钓鱼和加密挖掘等方式与恶意域进行通信
- DNS 攻击 - 与恶意 DNS 解析器进行通信 

[警报参考页](alerts-reference.md#alerts-dns)上提供了由适用于 DNS 的 Azure Defender 提供的警报的完整列表。

## <a name="dependencies"></a>依赖项

适用于 DNS 的 Azure Defender 不使用任何代理。 

若要保护 DNS 层，请按照[启用 Azure Defender](enable-azure-defender.md) 中的说明为每个订阅启用适用于 DNS 的 Azure Defender。


## <a name="next-steps"></a>后续步骤

本文介绍了适用于 DNS 的 Azure Defender。 如需相关材料，请参阅以下文章： 

- 安全警报可能是由安全中心生成的，也可能是由安全中心从其他安全产品接收的。 若要将所有这些警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。

- > [!div class="nextstepaction"]
    > [启用 Azure Defender](enable-azure-defender.md)