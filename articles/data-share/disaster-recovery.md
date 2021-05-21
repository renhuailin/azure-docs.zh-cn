---
title: 适用于 Azure Data Share 的灾难恢复
description: 适用于 Azure Data Share 的灾难恢复
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 1d7c9935d7e0d6bb2d457aa4c08f9b2b3e5fa910
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218691"
---
# <a name="disaster-recovery-for-azure-data-share"></a>适用于 Azure Data Share 的灾难恢复

本文介绍如何为 Azure Data Share 配置灾难恢复环境。 Azure 数据中心很少发生故障，但可能会持续数分钟到数小时之久。 数据中心中断可能会导致依赖于数据提供程序共享的数据的环境出现中断。 按照本文中详述的步骤操作，如果托管数据共享的主要区域发生数据中心中断，数据提供程序可以继续与数据使用者共享数据。 

## <a name="achieving-business-continuity-for-azure-data-share"></a>实现 Azure Data Share 的业务连续性

若要为数据中心中断做好准备，数据提供程序可以在次要区域中预配数据共享环境。 可以采取措施以确保能在数据中心产生中断时顺利地进行故障转移。 

数据提供程序可以将辅助 Azure Data Share 资源预配到其他区域。 可对这些 Data Share 资源进行配置，使其包含存在于主要 Azure Data Share 资源中的共享和数据集。 它们可以在配置 DR 环境时将数据使用者邀请到辅助共享，或者在以后的 手动故障转移中这样做。

如果数据使用者在辅助环境中预配了用于 DR 的活动共享订阅，则他们可以在故障转移时启用快照计划。 如果数据使用者不希望订阅用于进行 DR 的次要区域，可以之后再邀请他们加入辅助共享。 

数据使用者可以有一个用于 DR 的空闲活动共享订阅，或者数据提供程序可以在以后的手动故障转移过程中邀请数据使用者。 

## <a name="related-information"></a>相关信息

- [业务连续性和灾难恢复](../best-practices-availability-paired-regions.md)
- [将高可用性纳入 BCDR 策略](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。