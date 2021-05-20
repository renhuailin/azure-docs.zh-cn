---
title: Microsoft Teams 中的 Azure 实验室服务
description: 概述如何在 Microsoft Teams 中使用 Azure 实验室服务。
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f39613427ed174dfca80d4a48be9473ab7025e79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433913"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Microsoft Teams 中的 Azure 实验室服务

可以使用 Azure 实验室服务 Teams 应用在 Microsoft Teams 中利用 Azure 实验室服务。 任何拥有所有者/参与者/创建者对实验室帐户的访问权限的团队所有者都将能够创建实验室，并预配团队中每个人的的虚拟机。

本文概述在 Teams 中使用 Azure 实验室服务的好处，并提供指向有关如何在 Teams 中创建和管理实验室的说明的其他文章的链接。 

> [!NOTE]
>Azure 实验室服务 Teams 应用只能添加到团队中，不能添加到个人聊天或群组群聊中。

## <a name="benefits"></a>优点

Azure 实验室服务与 Microsoft Teams 的集成将帮助教师设置课堂环境并在团队中提供虚拟实验室环境（课程）： 

* 教师可以设置实验室，使学生可以从 Teams 中访问他们的 VM，而无需离开 Teams，也无需导航到 [Azure 实验室服务网站](https://labs.azure.com)。
* 从 Teams 到 Azure 实验室服务的单一登录 (SSO)。
* 团队和实验室所有者无需在两个不同的系统中维护班级名册 - 实验室用户列表从团队成员身份自动填充，并且每 24 小时自动执行一次同步。 
* 在首次发布模板 VM 之后，将根据从团队成员身份中添加/删除的用户数自动调整实验室容量（即实验室中的 VM 数）。 
* 团队和实验室所有者将仅查看与团队相关的实验室，而学生将仅查看为特定团队预配的 VM。 
* 发布实验室后，用户首次登录时将自动注册到实验室，并且自动分配 VM。 教师无需发送邀请，学生也无需单独注册实验室。  

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

- [开始在 Teams 中创建实验室](how-to-get-started-create-lab-within-teams.md)
- [在 Teams 中管理实验室用户列表](how-to-manage-user-lists-within-teams.md)
- [在 Teams 中管理实验室的 VM 池](how-to-manage-vm-pool-within-teams.md)
- [在 Teams 中创建和管理实验室计划](how-to-create-schedules-within-teams.md)
- [在 Teams 中访问 VM -“学生”视图](how-to-access-vm-for-students-within-teams.md)
