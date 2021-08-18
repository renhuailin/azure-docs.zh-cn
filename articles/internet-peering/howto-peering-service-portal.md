---
title: 使用 Azure 门户在直接对等互连的基础上启用 Azure 对等互连服务
titleSuffix: Azure
description: 使用 Azure 门户在直接对等互连的基础上启用 Azure 对等互连服务
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 29bf044648c2df44863b308105a762b086bbf6dd
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113688228"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>使用 Azure 门户在直接对等互连的基础上启用 Azure 对等互连服务

本文介绍如何使用 Azure 门户在直接对等互连的基础上启用 Azure [对等互连服务](overview-peering-service.md)。

如果需要，可以使用 [PowerShell](howto-peering-service-powershell.md) 完成本指南。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)。
* 在订阅中选择要启用对等互连服务的直接对等互连。 如果没有，请转换旧的直接对等互连，或创建新的直接对等互连：
    * 若要转换旧的直接对等互连，请按照[使用门户将旧的直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)中的说明进行操作。
    * 若要创建新的直接对等互连，请按照[使用门户创建或修改直接对等互连](howto-direct-portal.md)中的说明进行操作。

## <a name="enable-peering-service-on-a-direct-peering"></a>在直接对等互连上启用对等互连服务

### <a name="view-direct-peering"></a><a name= get></a>查看直接对等互连
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>为直接对等互连启用对等互连服务

在上一步中打开直接对等互连后，为其启用对等互连服务。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>修改直接对等互连连接

若要修改连接设置，请参阅[使用门户创建或修改直接对等互连](howto-direct-portal.md)中的“修改直接对等互连”部分。

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)
* [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他资源

有关常见问题解答，请参阅[对等互连服务常见问题解答](service-faqs.yml)。