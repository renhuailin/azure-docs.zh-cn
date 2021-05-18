---
title: 从 Teams 中删除 Azure 实验室服务实验室
description: 了解如何从 Teams 中删除 Azure 实验室服务实验室。
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 8d1e20f8f676eb9863187b550a3c0400871d670c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433948"
---
# <a name="delete-labs-within-teams"></a>在 Teams 中删除实验室

本文介绍如何从 Azure 实验室服务应用删除实验室。

## <a name="prerequisites"></a>先决条件

* 在 Azure 门户中[创建实验室服务帐户](tutorial-setup-lab-account.md#create-a-lab-account)。
* [开始使用并在 Teams 内创建实验室服务实验室](how-to-get-started-create-lab-within-teams.md)。

## <a name="delete-labs"></a>删除实验室

可以直接在[实验室服务网站](https://labs.azure.com)中删除实验室来删除在 Teams 中创建的实验室，如[管理 Azure 实验室服务中的实验室](how-to-manage-classroom-labs.md)中所述。 

删除团队时，也会触发实验室删除。 如果从中创建实验室的团队被删除，实验室将在自动用户列表同步触发后的 24 小时内自动删除。 

> [!IMPORTANT]
> 删除选项卡或卸载应用不会导致删除实验室。 

如果删除该选项卡，在团队成员列表中的用户仍能够访问[实验室服务网站](https://labs.azure.com)上的 VM，除非实验室删除是通过删除网站上的实验室或删除团队而被显式触发的。 

## <a name="next-steps"></a>后续步骤

- [在 Teams 中使用 Azure 实验室服务概述](lab-services-within-teams-overview.md)
- [在 Teams 中管理实验室用户列表](how-to-manage-user-lists-within-teams.md)
- [在 Teams 中管理实验室的 VM 池](how-to-manage-vm-pool-within-teams.md)
- [在 Teams 中创建和管理实验室计划](how-to-create-schedules-within-teams.md)
- [在 Teams 中访问 VM -“学生”视图](how-to-access-vm-for-students-within-teams.md)

