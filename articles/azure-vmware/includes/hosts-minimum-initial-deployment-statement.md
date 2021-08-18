---
title: 最小初始主机部署
description: 最精简的初始部署是三个主机。
ms.topic: include
ms.date: 04/28/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: d0efae1a8f3bf30360d2649b9d638f5cc800179f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747618"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-clouds-clusters.md -->

默认情况下，对于创建的每个私有云，均有一个 vSAN 群集。 可添加、删除和缩放群集。  每个群集和初始部署的最小主机数为三个。 

试用群集可用于评估并限制为每个私有云三台主机。

可以使用 vSphere 和 NSX-T Manager 来管理群集配置或操作的其他方面。 群集中每个主机的所有本地存储都受 vSAN 控制。

>[!TIP]
>如果你的需要超出初始部署数目，以后可以随时扩展群集和添加其他群集。
