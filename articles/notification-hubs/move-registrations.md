---
title: 将 Azure 通知中心资源从一个区域移到另一个区域
description: 了解如何将 Azure 通知中心资源移到其他 Azure 区域。
author: sethmanheim
ms.author: sethm
ms.service: notification-hubs
ms.topic: how-to
ms.date: 09/07/2021
ms.custom: template-how-to
ms.openlocfilehash: 0de15ab1f16ab5bc234ad6430eae5f65e608753e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594387"
---
# <a name="move-resources-between-azure-regions"></a>在 Azure 区域之间移动资源

本文介绍如何将 Azure 通知中心资源移到其他 Azure 区域。 此过程大致为：

1. 创建一个使用另一名称的目标命名空间。
1. 从前一命名空间导出注册。
1. 将注册导入所需区域的新命名空间。

## <a name="overview"></a>概述

在某些情况下，出于各种业务原因，你可能需要在 Azure 区域之间移动服务资源。 这些资源可能会移到新提供的区域，你可能希望部署仅在特定区域可用的功能或服务。移动是为了遵循内部策略或合规性要求，或者是为了解决容量问题。

Azure 通知中心命名空间名称是独一无二的，并且注册按中心进行，因此，若要执行此类移动，必须在所需区域创建新的中心，然后将注册以及所有其他相关的数据移到新创建的命名空间。

## <a name="create-a-notification-hubs-namespace-with-a-different-name"></a>创建使用其他名称的通知中心命名空间

按照以下步骤创建新的通知中心命名空间。 在“基本信息”选项卡中填写所有必需信息，包括命名空间的所需目标区域。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

创建新命名空间后，请确保设置新命名空间中的 PNS 凭据，并创建新命名空间中的等效策略。

## <a name="exportimport-registrations"></a>导出/导入注册

在要将资源移到其中的区域创建新命名空间后，批量导出所有注册，然后将它们导入到新命名空间中。 为此，请参阅[批量导出和导入 Azure 通知中心注册](export-modify-registrations-bulk.md)。

## <a name="delete-the-previous-namespace-optional"></a>删除前一命名空间（可选）

完成从旧命名空间到新命名空间的注册导出后，可以根据需要删除旧命名空间。

1. 转到前一区域的现有命名空间。

2. 单击“删除”，然后在“删除命名空间”窗格中重新输入命名空间名称。 

3. 单击“删除命名空间”窗格底部的“删除”。 

## <a name="next-steps"></a>后续步骤

以下文章是其他已提供区域移动文章的服务的示例。

- [将 NSG 移到另一区域](/azure/virtual-network/move-across-regions-nsg-portal)
- [将公共 IP 地址移到另一区域](/azure/virtual-network/move-across-regions-publicip-portal)
- [将存储帐户移到另一个区域](/azure/storage/common/storage-account-move?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)
- [（从资源组）跨区域移动资源](/azure/resource-mover/move-region-within-resource-group#:~:text=1%20In%20the%20Azure%20portal%2C%20open%20the%20relevant,you%20want%20to%20move.%20...%20More%20items...%20)
