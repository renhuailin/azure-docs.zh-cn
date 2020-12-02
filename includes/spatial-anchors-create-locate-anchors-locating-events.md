---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993101"
---
创建观察程序后，将为所请求的每个定位点触发 `AnchorLocated` 事件。 找到定位点或无法找到定位点时都会触发此事件。 如果发生这种情况，将在状态中说明原因。 在处理完观察程序的所有定位点（找到或未找到）后，将触发 `LocateAnchorsCompleted` 事件。 每个观察程序有 35 个标识符的限制。 
