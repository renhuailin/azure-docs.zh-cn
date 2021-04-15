---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92204450"
---
Azure 允许你在资源上放置锁，这样这些资源就无法被删除，或者会处于只读状态。 __锁定资源可能会导致意外结果。__ 某些操作看似不会修改资源，但实际上需要执行被锁阻止的操作。 

例如，将删除锁应用于工作区的资源组会阻止对 Azure ML 计算群集进行缩放操作。

若要详细了解如何锁定资源，请参阅[锁定资源以防止意外更改](../articles/azure-resource-manager/management/lock-resources.md)。