---
title: 计划性维护 - Azure Database for MySQL - 灵活服务器
description: 本文介绍了 Azure Database for MySQL 灵活服务器中的计划性维护功能。
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 0da77cb5291022357384fdf2e14e90fe76850f4f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652002"
---
# <a name="scheduled-maintenance-in-azure-database-for-mysql--flexible-server"></a>Azure Database for MySQL 灵活服务器中的计划性维护

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL 灵活服务器会执行定期维护，以确保托管数据库安全、稳定并处于最新状态。 在维护期间，服务器会获取新的功能、更新和补丁。

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器目前处于预览版阶段。

## <a name="select-a-maintenance-window"></a>选择维护时段

可以计划在一周中特定某天以及该天某个时段范围内进行维护。 或者，你可以让系统自动选择某一天和某个时段。 无论采用哪种方式，系统都会在运行任何维护之前提前 5 天提醒你。 系统还会在维护开始时以及在维护成功完成时告知你。

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

你可以随时更新计划设置。 如果你为灵活服务器计划了维护，并且更新了计划首选项，则当前推出的维护将按计划继续进行，并在其成功完成后为下次计划性维护应用计划设置更改。

可以为 Azure 订阅中的每个灵活服务器定义系统管理的计划或自定义计划。  
* 使用自定义计划时，可以通过选择星期几和一小时时间范围来为服务器指定维护时段。  
* 使用系统管理的计划时，系统会在服务器的区域时间中选择介于晚上 11 点与上午 7 点之间的任意一小时的时间。  

在推出更改的过程中，我们先将更新应用于使用系统管理的计划所配置的服务器，随后在给定区域中最少 7 天的间隔之后，应用于具有自定义计划的服务器。 如果要在开发和测试环境服务器队列中收到早期更新，我们建议为开发和测试环境中使用的服务器配置系统管理的计划。 这样便可以在开发/测试环境中首先收到最新更新，便于测试和评估以进行验证。 如果遇到任何行为更改或重大更改，则在将相同更新推出到具有自定义管理计划的生产服务器之前，你有时间处理这些问题。 更新会在 7 天后开始在自定义计划的灵活服务器上推出，并在定义的维护时段内应用于服务器。 目前，发送通知后，无法选择延迟更新。 建议仅将自定义计划用于生产环境。 

在极少数情况下，维护事件可能会被系统取消，或者可能无法成功完成。 如果更新失败，则会还原更新，并还原以前版本的二进制文件。 在这种失败的更新情况中，你可能仍会在维护时段内遇到服务器重启。 如果更新已取消或失败，系统会创建有关已取消或失败维护事件的通知，并发送相应的通知。 会根据当前计划设置安排下次维护，并且你会提前五天收到相关通知。 

## <a name="next-steps"></a>后续步骤

* 了解如何[更改维护计划](how-to-maintenance-portal.md)
* 了解如何使用 Azure 服务运行状况[获取有关即将进行的维护的通知](../../service-health/service-notifications.md)
* 了解如何[设置有关即将发生的计划性维护事件的警报](../../service-health/resource-health-alert-monitor-guide.md)
