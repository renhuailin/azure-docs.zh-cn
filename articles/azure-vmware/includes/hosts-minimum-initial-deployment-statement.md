---
title: 最小初始主机部署
description: 最精简的初始部署是三个主机。
ms.topic: include
ms.date: 09/29/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 56a2f12ae7dddc6e0783d715554934d48dd227b1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300337"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-clouds-clusters.md -->

默认情况下，对于创建的每个私有云，均有一个 vSAN 群集。 可添加、删除和缩放群集。  每个群集和初始部署的最小主机数为三个。 

可以使用 vSphere 和 NSX-T Manager 来管理群集配置或操作的其他方面。 群集中每个主机的所有本地存储都受 vSAN 控制。

>[!TIP]
>如果你的需要超出初始部署数目，以后可以随时扩展群集和添加其他群集。
