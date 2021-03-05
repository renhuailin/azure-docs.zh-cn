---
title: Azure 事件网格中的事件域用例
description: 本文介绍了在 Azure 事件网格中使用事件域的几个用例。
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 00318fc78053ed55e3599c329746d89d2eee4f99
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204296"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Azure 事件网格中的事件域用例
本文介绍了在 Azure 事件网格中使用事件域的几个用例。 

## <a name="use-case-1"></a>用例 1 
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>用例 2
使用系统主题时，最大限制为500事件订阅。 如果系统主题需要500个以上的事件订阅，可以使用域。 

假设你已为 Azure Blob 存储创建了一个系统主题，并且你需要创建超过500的订阅，但由于每个主题)  (500 订阅的限制，这是不可能的。 在这种情况下，可以使用以下使用事件域的解决方案。 

1. 创建一个可支持多达100000个主题的域，每个域主题可以有500个事件订阅。 此模型将为你生成 500 * 100000 事件订阅。 
1. 为 Azure 存储系统主题创建 Azure function 订阅。 当函数从 Azure 存储接收事件时，它可以将事件扩充并发布到适当的域主题。 例如，函数可以分析 blob 文件名，以确定目标域主题，并将事件发布到域主题。 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="AzureEvent Grid 域-用例2":::


## <a name="next-steps"></a>后续步骤
若要了解有关设置事件域、创建主题、创建事件订阅和发布事件的信息，请参阅[管理事件域](./how-to-event-domains.md)。
