---
title: 在 Azure Data Share 中撤销共享订阅
description: 了解如何用 Azure Data Share 撤销收件人的共享订阅。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 102a0099c95073793ba6a1a85f518c7539327bf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87511847"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>如何在 Azure Data Share 中撤销使用者的共享订阅

本文介绍如何用 Azure Data Share 撤销一个或多个使用者的共享订阅。 这会防止使用者继续触发快照。 使用者如果尚未触发快照，则在撤销共享订阅后永远不会收到数据。 如果先前已触发了快照，则所拥有的最新数据将保留在其帐户中。

## <a name="navigate-to-a-sent-data-share"></a>导航到已发送的数据共享

在 Azure Data Share 中，导航到已发送的共享并选择“共享订阅”选项卡。

![撤销共享订阅](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

选中要删除其共享订阅的收件人旁边的复选框，然后单击“撤消”。 使用者将不再获取其数据的更新。

## <a name="next-steps"></a>后续步骤
详细了解如何[监视数据共享](how-to-monitor.md)。