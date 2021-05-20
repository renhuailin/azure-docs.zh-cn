---
title: 设置与 Microsoft 进行对等互连的先决条件
titleSuffix: Azure
description: 设置与 Microsoft 进行对等互连的先决条件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bc45bc8809eabe75902b602835ea595b56ff3cf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586834"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>设置与 Microsoft 进行对等互连的先决条件

在请求新的对等互连或将旧的对等互连转换为 Azure 资源之前，请确保满足以下先决条件。

## <a name="azure-related-prerequisites"></a>与 Azure 相关的先决条件
* Microsoft Azure 帐户：如果没有 Microsoft Azure 帐户，请创建 [Microsoft Azure 帐户](https://azure.microsoft.com/free)。 若要设置对等互连，则需要有效的和活动的 Microsoft Azure 订阅，因为对等互连将建模为 Azure 订阅中的资源。 请务必注意以下几点：
    * 用于设置对等互连的 Azure 资源类型始终为免费的 Azure 产品，即无需支付创建 Azure 帐户、创建订阅或访问 Azure 资源 PeerAsn 和对等互连来建立对等互连的费用。 这不会与你和 Microsoft 之间的直接对等互连的对等互连协议混淆，其中的条款已与我们的对等互连团队明确讨论。 如果此方面有任何问题，请联系 [Microsoft 对等互连](mailto:peering@microsoft.com)。
    * 你可以使用相同的 Azure 订阅来访问其他可能免费或付费的 Azure 产品或云服务。 访问付费产品时，会产生费用。
    * 如果要创建新的 Azure 帐户和/或订阅，则你可能在试用期间有资格获得免费的 Azure 额度，可以利用该额度试用 Azure 云服务。 如果感兴趣，请访问 [Microsoft Azure 帐户](https://azure.microsoft.com/free)获取详细信息。

* 关联对等 ASN：请求对等互连之前，请先将 ASN 和联系人信息关联到订阅。 按照[将对等 ASN 关联到 Azure 订阅](howto-subscription-association-powershell.md)中的说明进行操作。

## <a name="other-prerequisites"></a>其他先决条件
* PeeringDB 配置文件：对等体应在 [PeeringDB](https://www.peeringdb.com) 上具有完整且最新的配置文件。 我们在注册系统中使用此信息来验证对等体的详细信息，例如 NOC 信息、技术联系信息以及它们在对等设施中是否存在等。

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改直接对等互连](howto-direct-portal.md)。
* [使用门户将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)
* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)
* [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)