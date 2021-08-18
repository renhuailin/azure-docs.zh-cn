---
title: Azure 事件网格中事件域的用例
description: 本文介绍了在 Azure 事件网格中使用事件域的几个用例。
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 24a338717b44567bad0ec1575d98ddaeada71113
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413614"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Azure 事件网格中事件域的用例
本文介绍了在 Azure 事件网格中使用事件域的几个用例。 

## <a name="use-case-1"></a>用例 1 
[!INCLUDE [event-grid-domain-example-use-case.md](./includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>用例 2
使用系统主题时，最多只能有 500 个事件订阅。 如果系统主题需要 500 个以上的事件订阅，可以使用域。 

假设你已为 Azure Blob 存储创建了一个系统主题，并且你需要为该主题创建 500 个以上的订阅，但是由于限制（每个主题 500 个订阅）而无法实现。 在这种情况下，可使用以下使用事件域的解决方案。 

1. 创建一个可支持多达 100,000 个主题的域，每个域主题可以有 500 个事件订阅。 此模型将为你生成 500 * 100,000 个事件订阅。 
1. 为 Azure 存储系统主题创建 Azure 函数订阅。 当函数从 Azure 存储接收事件时，它可以将事件扩充并发布到适当的域主题。 例如，函数可以分析 Blob 文件名来确定目标域主题，并将事件发布到域主题。 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="Azure 事件网格域 - 用例 2":::


## <a name="next-steps"></a>后续步骤
若要了解有关设置事件域、创建主题、创建事件订阅和发布事件的信息，请参阅[管理事件域](./how-to-event-domains.md)。
