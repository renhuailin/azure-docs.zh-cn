---
title: 为高级 Azure 事件中心命名空间配置处理单元
description: 提供为高级层事件中心命名空间配置处理单元的说明。
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: 443832c9fcd4ee8ce8e314a80251f2575aed003d
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110631547"
---
# <a name="configure-processing-units-for-a-premium-tier-azure-event-hubs-namespace"></a>为高级层 Azure 事件中心命名空间配置处理单元
本文提供为高级层 Azure 事件中心命名空间配置处理单元 (PU) 的说明。 若要详细了解高级层，请参阅[事件中心高级层](event-hubs-premium-overview.md)。

## <a name="configure-processing-units-when-creating-a-namespace"></a>在创建命名空间时配置处理单元
在 Azure 门户中创建高级层命名空间时，会自动启用自动扩充功能。 可以为命名空间配置处理单元 (PU) 数（1、2、4、8 或 16），并且可以在创建命名空间后更新该数字。 

:::image type="content" source="./media/configure-processing-units-premium-namespace/event-hubs-auto-inflate-prem.png" alt-text="在创建高级命名空间时进行启用的屏幕截图":::

## <a name="configure-processing-units-for-an-existing-namespace"></a>配置现有命名空间的处理单元
若要更新现有高级命名空间的 PU 数，请执行以下步骤： 

1. 在你的命名空间的“事件中心命名空间”页上，选择左侧菜单的“设置”下的“缩放”  。 
1. 更新处理单元的值，然后选择“保存” 。 

    :::image type="content" source="./media/configure-processing-units-premium-namespace/scale-settings-prem.png" alt-text="为现有高级命名空间启用自动扩充的屏幕截图":::

## <a name="next-steps"></a>后续步骤
若要详细了解处理单元和事件中心高级层，请参阅以下文章。

- [事件中心高级层](event-hubs-premium-overview.md)
- [事件中心可伸缩性](event-hubs-scalability.md)
