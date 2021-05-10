---
title: 对等互连服务合作伙伴演练
titleSuffix: Azure
description: 对等互连服务合作伙伴演练
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 292c850f5352c80112e3f0bcc5c868f8128819be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592307"
---
# <a name="peering-service-partner-walkthrough"></a>对等互连服务合作伙伴演练

本部分介绍提供商为对等互连服务启用直接对等互连所需遵循的步骤。

## <a name="create-direct-peering-connection-for-peering-service"></a>为对等互连服务创建直接对等互连连接
服务提供商可以通过新建支持对等互连服务的直接对等互连来扩大其地理覆盖范围。 为此，请执行以下操作：
1. 需成为对等互连服务合作伙伴（如果尚不是）
1. 按照说明[使用门户创建或修改直接对等互连](howto-direct-portal.md)。 确保它满足高可用性要求。
1. 然后，按照相应步骤[使用门户在直接对等互连上启用对等互连服务](howto-peering-service-portal.md)。

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>为对等互连服务使用旧直接对等互连连接
如果要使用旧直接对等互连为对等互连服务提供支持，请执行以下操作：
1. 需成为对等互连服务合作伙伴（如果尚不是）。
1. 按照说明[使用门户将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)。 根据需要订购额外线路以满足高可用性要求。
1. 然后，按照相应步骤[使用门户在直接对等互连上启用对等互连服务](howto-peering-service-portal.md)。

## <a name="next-steps"></a>后续步骤

* 了解[对等互连策略](https://peering.azurewebsites.net/peering)。
* 若要了解如何通过相关步骤设置与 Microsoft 的直接对等互连，请按[直接对等互连演练](walkthrough-direct-all.md)进行操作。
* 若要了解如何通过相关步骤设置与 Microsoft 的 Exchange 对等互连，请按 [Exchange 对等互连演练](walkthrough-exchange-all.md)进行操作。