---
title: VMware 软件更新频率
description: Azure VMware 解决方案支持的 VMware 软件更新频率。
ms.topic: include
ms.date: 03/22/2021
ms.openlocfilehash: a3290ed704b493fae3e86223857ff9f1c5617f1a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869856"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Microsoft 负责 Azure VMware 解决方案私有云中的 VMware 软件（ESXi、vCenter、PSC 和 NXS）的生命周期管理。

私有云软件按计划升级，该计划将跟踪 VMware 发布的软件捆绑包。 私有云无需停机即可升级。

私有云软件捆绑包升级的目的是使软件与 VMware 最新发布的版软件捆绑包保持同一版本。 私有云软件版本可能不同于各个软件组件（ESXi、NSX-T、vCenter、vSAN）的最新版本。

软件更新包括：

- 补丁 - VMware 发布的安全补丁或 bug 修补程序
- 更新 - VMware 堆栈组件的次要版本更改
- 升级 - VMware 堆栈组件的主要版本更改

Microsoft 可以在 VMware 发布关键安全补丁后立即对其进行测试。

在部署下一个计划的更新之前，将会实施有文档记录的 VMware 解决方法，而不是安装相应的补丁。 
