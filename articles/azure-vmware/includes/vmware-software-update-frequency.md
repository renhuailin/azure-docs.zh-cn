---
title: VMware 软件更新频率
description: Azure VMware 解决方案支持的 VMware 软件更新频率。
ms.topic: include
ms.date: 04/23/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 3295ab9da93b1e1ec1cace62653670de366c880d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324311"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

主机维护和生命周期管理对私有云群集的容量或性能没有影响。 私有云软件按计划升级，该计划将跟踪 VMware 发布的软件捆绑包。  因此，私有云无需停机即可升级。

私有云软件捆绑包升级的目的是使软件与 VMware 最新发布的版软件捆绑包保持同一版本。 私有云软件版本可能不同于各个软件组件（ESXi、NSX-T、vCenter、vSAN）的最新版本。 更新还包括驱动程序、网络交换机上的软件和裸机节点上的固件。

在将修补程序应用到私有云之前和之后，你都会收到通知。 在将更新或升级应用到私有云之前，我们还将与你共同计划维护时段。 

软件更新包括：

- 补丁 - VMware 发布的安全补丁或 bug 修补程序
- 更新 - VMware 堆栈组件的次要版本更改
- 升级 - VMware 堆栈组件的主要版本更改

>[!NOTE]
>Microsoft 可以在 VMware 发布关键安全补丁后立即对其进行测试。

在部署下一个计划的更新之前，将会实施有文档记录的 VMware 解决方法，而不是安装相应的补丁。
