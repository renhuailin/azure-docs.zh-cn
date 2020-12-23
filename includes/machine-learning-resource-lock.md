---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204450"
---
Azure 允许你对资源进行 _锁定_ ，以便不能删除或只读资源。 __锁定资源可能会导致意外的结果。__ 某些看起来不修改资源的操作实际上需要锁阻止的操作。 

例如，对工作区的资源组应用删除锁定会阻止 Azure ML 计算群集的缩放操作。

有关锁定资源的详细信息，请参阅 [锁定资源以防止意外更改](../articles/azure-resource-manager/management/lock-resources.md)。