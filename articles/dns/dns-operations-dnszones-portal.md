---
title: 在 Azure DNS 中管理 DNS 区域 - Azure 门户 | Microsoft Docs
description: 可以使用 Azure 门户管理 DNS 区域。 本文介绍如何在 Azure DNS 上更新、删除和创建 DNS 区域
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/28/2021
ms.author: rohink
ms.openlocfilehash: 58118480c16f2e318bab7435a79e27629880acc5
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108203177"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>如何在 Azure 门户中管理 DNS 区域

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure 经典 CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

本文介绍如何使用 Azure 门户管理 DNS 区域。 也可使用跨平台的 [Azure CLI](dns-operations-dnszones-cli.md) 或 Azure [PowerShell](dns-operations-dnszones.md) 管理 DNS 区域。

## <a name="create-a-dns-zone"></a>创建 DNS 区域

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在屏幕左上方，选择“创建资源”，然后搜索“DNS 区域”。 然后选择“创建”。

    :::image type="content" source="./media/dns-operations-dnszones-portal/search-dns-zone.png" alt-text="为 DNS 区域创建资源搜索的屏幕截图。":::

1. 在“创建 DNS 区域”页上输入以下值，然后选择“创建”：

    | 设置 | 详细信息 |
    | --- | --- |
    | **订阅** | 选择要在其中创建 DNS 区域的订阅。|
    | **资源组** | 选择或创建新资源组。 若要详细了解资源组，请阅读 [Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) 这篇概述文章。|
    | **名称** | 输入 DNS 区域的名称。 例如 contoso.com。 |
    | **位置** | 选择资源组所在的位置。 如果使用以前创建的资源组，则已选择该位置。  |

> [!NOTE]
> 资源组指的是资源组的位置，对 DNS 区域没有影响。 DNS 区域位置始终是“全局”，并且不会显示。

## <a name="list-dns-zones"></a>列出 DNS 区域

在 Azure 门户顶部的搜索资源中，搜索“DNS 区域”。 每个 DNS 区域都是其自己的资源。 可从该页面查看记录集的数目和名称服务器等信息。 默认视图中无“名称服务器”一列。 若要添加该列，请选择“托管视图”>“编辑列”>“+添加列”，然后从下拉列表中选择“名称服务器”。 选择“保存”以应用新列。

:::image type="content" source="./media/dns-operations-dnszones-portal/list-zones.png" alt-text="DNS 区域列表页面的屏幕截图。":::

## <a name="delete-a-dns-zone"></a>删除 DNS 区域

在门户中导航到 DNS 区域。 在所选的“DNS 区域”概述页面，选择“删除区域”。 然后系统会提示你确认是否要删除 DNS 区域。 删除 DNS 区域还会删除该区域中包含的所有记录。

:::image type="content" source="./media/dns-operations-dnszones-portal/delete-zone.png" alt-text="概述页面的“删除 DNS 区域”按钮的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

访问[通过 Azure 门户开始使用 Azure DNS](dns-getstarted-portal.md)，可了解如何处理 DNS 区域和记录。
