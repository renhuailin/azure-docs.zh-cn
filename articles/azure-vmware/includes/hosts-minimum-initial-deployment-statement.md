---
title: 最小初始主机部署
description: 最精简的初始部署是三个主机。
ms.topic: include
ms.date: 04/28/2021
ms.openlocfilehash: 32ed19a8ac3535f407549bea87acb6b433004b02
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111431040"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-clouds-clusters.md -->

默认情况下，对于创建的每个私有云，均有一个 vSAN 群集。 可添加、删除和缩放群集。  每个群集和初始部署的最小主机数为三个。 

试用群集可用于评估并限制为每个私有云三台主机。

可以使用 vSphere 和 NSX-T Manager 来管理群集配置或操作的其他方面。 群集中每个主机的所有本地存储都受 vSAN 控制。

>[!TIP]
>如果你的需要超出初始部署数目，以后可以随时扩展群集和添加其他群集。
