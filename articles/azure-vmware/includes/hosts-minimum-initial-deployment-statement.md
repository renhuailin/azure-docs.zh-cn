---
title: 最小初始主机部署
description: 最精简的初始部署是三个主机。
ms.topic: include
ms.date: 04/27/2021
ms.openlocfilehash: 72f0ad6c02c7ef24301276f1c6f68b300c86c0a4
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108126297"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-clouds-clusters.md -->


默认情况下，对于创建的每个私有云，均有一个 vSAN 群集。 可添加、删除和缩放群集。  每个群集的最小主机数目为 3 个。 可逐个添加更多的主机，每个群集最多可以包含 16 个主机。 每个私有云的最大群集数目为 12 个。  Azure VMware 解决方案的初始部署有 3 台主机。 

试用群集可用于评估并限制为 3 台主机。 每个私有云有一个试用群集。 可以在评估期内按单个主机缩放试用群集。

可以使用 vSphere 和 NSX-T Manager 来管理群集配置或操作的其他方面。 群集中每个主机的所有本地存储都受 vSAN 控制。

>[!TIP]
>如果你的需要超出初始部署数目，以后可以随时扩展群集和添加其他群集。