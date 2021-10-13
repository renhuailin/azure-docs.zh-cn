---
title: 最小初始主机部署
description: 最精简的初始部署是三个主机。
ms.topic: include
ms.date: 09/29/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 76b24de732f9668580d5a42f4279d9882a9ae702
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638163"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-clouds-clusters.md -->

默认情况下，对于创建的每个私有云，均有一个 vSAN 群集。 可添加、删除和缩放群集。  每个群集和初始部署的最小主机数为三个。 

可以使用 vSphere 和 NSX-T Manager 来管理群集配置或操作的其他方面。 群集中每个主机的所有本地存储都受 vSAN 控制。

>[!TIP]
>如果你的需要超出初始部署数目，以后可以随时扩展群集和添加其他群集。
