---
title: Azure VMware Solution by CloudSimple - 提升 CloudSimple 权限
description: 介绍如何提升 CloudSimple 权限以在私有云 vCenter 中执行管理功能
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0fa83dbec3c5d3ea7547d5b04ee395e09a96164
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618622"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>提升 CloudSimple 权限以在私有云 vCenter 中执行管理功能

CloudSimple 权限方法旨在为 vCenter 用户授予执行正常操作所需的权限。 在某些情况下，用户可能需要额外的权限来执行特定任务。  你可以在有限的时间范围内提升 vCenter SSO 用户的权限。

提升权限的原因包括以下几点：

* 配置标识源
* 用户管理
* 删除分布式端口组
* 安装 vCenter 解决方案（例如备份应用）
* 创建服务帐户

> [!WARNING]
> 在提升权限状态下执行的操作可能会对系统产生负面影响，还可导致系统不可用。 在提升期间仅执行必要的操作。

在 CloudSimple 门户中，为 CloudOwner 的vCenter SSO 本地用户[提升权限](escalate-private-cloud-privileges.md)。  仅当在 vCenter 上配置了其他标识提供者时，才能提升远程用户的权限。  提升权限涉及将选定的用户添加到 vSphere 内置管理员组。  只有一个用户能拥有提升的权限。  如需提升其他用户的权限，请先解除提升当前用户的权限。

必须将来自其他标识源的用户添加为 CloudOwner 组的成员。

> [!CAUTION]
> 新用户只能添加到“云所有者组”、“云全局群集管理组”、“云全局存储管理组”、“云全局网络管理组”或“云全局 VM 管理组”。  添加到“管理员”组的用户将被自动删除。  服务帐户只能添加到“管理员”组，且服务帐户不得用于登录 vSphere Web UI。

在提升期间，CloudSimple 结合使用自动监视和关联的警报通知，用以识别对环境进行的任何意外更改。
