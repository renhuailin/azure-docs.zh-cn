---
title: vCenter 访问和标识简介
description: vCenter 具有名为 cloudadmin 的内置本地用户，并为该用户分配了 CloudAdmin 角色。
ms.topic: include
ms.date: 08/31/2021
ms.openlocfilehash: 1fdc81c11b833b1239d37f613732eec42b463e1a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252528"
---
<!-- used in concepts-run-commands.md and tutorial-configure-identity-source.md -->

在 Azure VMware 解决方案中，vCenter 具有名为 cloudadmin 的内置本地用户，并为该用户分配了 CloudAdmin 角色。 你可以使用私有云的 CloudAdmin 角色在 Active Directory (AD) 中配置用户和组。 通常，CloudAdmin 角色会在私有云中创建和管理工作负载。 但在 Azure VMware 解决方案中，CloudAdmin 角色具有与其他 VMware 云解决方案和本地部署中不同的 vCenter 特权。

>[!IMPORTANT]
>本地 cloudadmin 用户应被视为私有云中“破窗”场景的紧急访问帐户。 它不用于日常管理活动或与其他服务的集成。 

- 在 vCenter 和 ESXi 本地部署中，管理员有权访问 vCenter \@ 帐户。 还可以为其分配更多的 AD 用户和组。 

- 在 Azure VMware 解决方案部署中，管理员无权访问管理员用户帐户。 但是，他们可以将 AD 用户和组分配到 vCenter 中的 CloudAdmin 角色。  CloudAdmin 角色无权将本地 LDAP 或 LDAPS 服务器等标识源添加到 vCenter。 但是，你可以使用“运行”命令添加标识源，并将 cloudadmin 角色分配给用户和组。
 
私有云用户无法访问 Microsoft 支持和管理的特定管理组件，也无法对其进行配置。 例如，群集、主机、数据存储和分布式虚拟交换机。

>[!NOTE]
>在 Azure VMware 解决方案中，vsphere.local SSO 域作为托管资源提供，以支持平台操作。 它不支持创建和管理本地组和用户，默认情况下通过私有云提供的本地组和用户除外。
