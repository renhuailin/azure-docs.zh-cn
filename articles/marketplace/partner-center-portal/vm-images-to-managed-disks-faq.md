---
title: 我们正在将虚拟机 (VM) 映像迁移到 Azure 市场中的托管磁盘存储
description: 为了给新的商城特性和功能提供更快、更可靠的存储和支持，我们要将商城 VM 映像迁移到托管磁盘存储。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
author: kriti-ms
ms.author: krsh
ms.openlocfilehash: e3d20b5e9206cf05d749f75982a07937a8caf078
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201084"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>我们正在将虚拟机 (VM) 映像迁移到 Azure 市场中的托管磁盘存储

为了给新的商城特性和功能提供更快、更可靠的存储和支持，我们要将商城 VM 映像迁移到托管磁盘存储。

我们会从 2020 年 1 月 2 日开始分阶段将 VM 映像迁移到托管磁盘存储。 在第一阶段，我们将只移动在头 90 天内没有新部署或没有正在运行的 VM 的映像。 在移动任何映像之前，我们都会发送电子邮件，让发布者知道将会移动哪些映像以及何时移动。

发布者或使用者不需要执行任何操作，用户也不会受到影响。 商城产品/服务将会保持可用状态。在移动过程中和移动之后，客户仍将能够从这些映像部署托管 VM。

如有任何问题，请[联系我们](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)。

## <a name="faqs"></a>常见问题

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>我的 VM 映像的用户是否会遇到中断？

VM 映像的用户将不会遇到中断。 

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>完成该过程需要多久？

完成迁移最多需要 24 小时。

### <a name="do-i-need-to-take-any-action"></a>我需要执行什么操作吗？

不知道。 发布者或使用者不需要执行任何操作。

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>在云门户 API 迁移到托管磁盘存储后，我是否必须更新系统，以便用不同方式调用这些 API？

不知道。 现有的 API 调用将会继续有效。

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>是否我所有的 VM 映像都会同时迁移到托管磁盘？

我们将在同一天移动所有的 VM 映像。 在移动了这些映像后，我们会通知你。

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>我能否请求将我的 VM 映像迁移安排到以后？

我们建议按计划的日期迁移映像。 但是，如果你有顾虑，请联系我们重新安排迁移时间。

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>在迁移过程中，是否可以发布对 VM 映像的更新？

在迁移过程中，无法对 VM 映像进行更新。

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>在 VM 映像迁移到托管磁盘后，发布过程是否会更改？

不会，发布过程将保持不变。 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>发布者是否可以将其产品/服务迁移到托管磁盘？

不可以，发布者无法将其产品/服务迁移到托管磁盘。 他们需要等待，其映像会自动迁移。 在进行任何更改之前，我们都会向发布者发送通知。
