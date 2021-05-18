---
title: 网络限制提高
description: 网络限制提高
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e945dec2ce8514c3e4f1edecdecd13c5c43f7c75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745445"
---
# <a name="networking-limit-increase"></a>网络限制提高

使用 [Azure 门户](https://portal.azure.com)增加网络配额。

要在 Azure 门户中查看当前网络使用情况和配额，请打开订阅，然后选择“使用情况 + 配额”。 你还可以使用以下选项来查看网络使用情况和限制。

* [使用情况 CLI](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [网络使用情况 API](/rest/api/virtualnetwork/virtualnetworks/listusage)

可以在门户中使用“帮助 + 支持”或“使用情况 + 配额”来请求增加。 

> [!Note]
> 要更改公共 IP 前缀的默认大小，请从下拉列表中选择“公共 Ip 网际网络前缀最小长度”。 

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>使用“帮助 + 支持”在订阅级别增加请求网络配额

按照以下说明在 Azure 门户中使用“帮助 + 支持”创建支持请求。

1. 登录到 [Azure 门户](https://portal.azure.com)，然后从 Azure 门户菜单中选择“帮助 + 支持”，或者搜索并选择“帮助 + 支持”。 

    ![帮助 + 支持](./media/networking-quota-request/help-plus-support.png)

1. 选择“新建支持请求”。

    ![新建支持请求](./media/networking-quota-request/new-support-request.png)

1. 对于“问题类型”，选择“服务和订阅限制（配额）”。 

    ![从问题类型下拉列表中选择订阅限制](./media/networking-quota-request/select-quota-issue-type.png)

1. 选择需要增加配额的订阅。

    ![选择订阅 newSR](./media/networking-quota-request/select-subscription-support-request.png)

1. 在“配额类型”下，选择“网络”。  选择“下一步: 解决方案”。

    ![选择配额类型](./media/networking-quota-request/select-quota-type-network.png)

1. 在“问题详细信息”中，选择“提供详细信息”并填写其他信息，以帮助处理你的请求。 

    ![提供详细信息](./media/networking-quota-request/provide-details-link.png)

1. 在“配额详细信息”面板中，选择要在请求中包含的部署模型、位置和资源。

    ![配额详细信息 DM](./media/networking-quota-request/quota-details-network.png)

1. 输入想对订阅设定的新限制。 要删除一行，请从“资源”菜单中取消选择资源，或选择废弃图标“x”。 输入每个资源的配额后，选择“保存并继续”以继续创建支持请求。

    ![新限制](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>使用“使用情况 + 配额”在订阅级别增加请求网络配额

按照以下说明在 Azure 门户中使用“使用情况 + 配额”创建支持请求。

1. 从 https://portal.azure.com 中，搜索并选择“订阅”。

    ![订阅](./media/networking-quota-request/search-for-suscriptions.png)

1. 选择需要增加配额的订阅。

    ![选择订阅](./media/networking-quota-request/select-subscription-change-quota.png)

1. 选择“使用情况 + 配额” 

    ![选择使用情况和配额](./media/networking-quota-request/select-usage-plus-quotas.png)

1. 在右上角选择“请求增加”  。

    ![请求增加](./media/networking-quota-request/request-increase-from-subscription.png)

1. 从[在订阅级别请求网络配额增加](#request-networking-quota-increase-at-subscription-level-using-help--support)中的步骤 3 开始，按步骤操作。

## <a name="about-networking-limits"></a>关于网络限制

要了解有关网络限制的更多信息，请参阅限制页面的[网络部分](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)或我们的网络限制常见问题解答。
