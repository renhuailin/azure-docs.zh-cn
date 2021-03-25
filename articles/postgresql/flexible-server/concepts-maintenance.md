---
title: 计划性维护 - Azure Database for PostgreSQL 灵活服务器
description: 本文介绍了 Azure Database for PostgreSQL 灵活服务器中的计划性维护功能。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ffee15776a48b6495f78b6becf81c620e1dc4d69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336303"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Azure Database for PostgreSQL 灵活服务器中的计划性维护
 
Azure Database for PostgreSQL 灵活服务器执行定期维护，以确保托管数据库安全、稳定并处于最新状态。 在维护期间，服务器会获取新的功能、更新和补丁。
 
> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供
 
## <a name="selecting-a-maintenance-window"></a>选择维护时段
 
你可以计划在一周中特定某天以及当天某个时段范围内进行维护。 或者，你可以让系统自动选择某一天和某个时段。 无论采用哪种方式，系统都会在运行任何维护之前提前 5 天提醒你。 系统还会在维护开始时以及在维护成功完成时告知你。
 
有关即将开始的计划性维护的通知可能会：
 
* 通过电子邮件发送到特定地址
* 通过电子邮件发送到 Azure 资源管理器角色
* 以短信 (SMS) 形式发送到移动设备
* 作为通知推送到 Azure 应用
* 作为语音消息传递
 
为维护计划指定首选项时，可以选择一周中的某一天，然后选择一个时间范围。 如果未指定，系统将选择服务器区域时间中的晚上 11 点到早上 7 点之间的时间。 你可以为 Azure 订阅中的每个灵活服务器定义不同的计划。 
 
> [!IMPORTANT]
> 通常，服务器的成功计划性维护事件间隔至少为 30 天。
>
> 但如果发生关键紧急更新（如严重漏洞），通知时间范围可能会短于五天。 即使在过去 30 天内成功地执行了计划性维护，关键更新也可能会应用于服务器。

你可以随时更新计划设置。 如果你为灵活服务器计划了维护，并且更新了计划首选项，则当前事件将会按计划继续进行，而计划设置的更改将会在该事件成功完成之后生效。 

如果维护事件被系统取消或无法成功完成，则系统将分别创建有关已取消或失败的维护事件的通知。 下次执行维护的尝试将会根据当前计划设置进行计划，并且你会提前五天收到相关通知。
 
## <a name="next-steps"></a>后续步骤
 
* 了解如何[更改维护计划](how-to-maintenance-portal.md)
* 了解如何使用 Azure 服务运行状况[获取有关即将进行的维护的通知](../../service-health/service-notifications.md)
* 了解如何[设置有关即将发生的计划性维护事件的警报](../../service-health/resource-health-alert-monitor-guide.md)
