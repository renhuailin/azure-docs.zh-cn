---
title: Azure Red Hat OpenShift 群集管理员角色 | Microsoft Docs
description: 分配和使用 Azure Red Hat OpenShift 群集管理员角色
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f4fc57ebe39a2169ea91423b295f4bc436746b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100636231"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 客户管理员角色

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将于 2022 年 6 月 30 日停用。 对新建 Azure Red Hat OpenShift 3.11 群集这一功能的支持会持续到 2020 年 11 月 30 日。 在停用之后，剩余的 Azure Red Hat OpenShift 3.11 群集将会关闭，以防出现安全漏洞。
> 
> 请按照本指南[创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。
> 如有具体问题，[请与我们联系](mailto:arofeedback@microsoft.com)。

你是 Azure Red Hat OpenShift 群集的群集管理员。 你的帐户已经增加了针对所有用户创建项目的权限和访问权限。

当帐户具有绑定的“客户-管理员-群集”授权角色时，它可以自动管理项目。

> [!Note] 
> “客户-管理员-群集”群集角色与“群集-管理员”群集角色不同。

例如，可以执行与一组谓词 (`create`) 关联的操作，以便对一组资源名称 (`templates`) 执行操作。 若要查看这些角色及其谓词组和资源组的详细信息，请运行以下命令：

`$ oc get clusterroles customer-admin-cluster -o yaml`

谓词名称不一定都会直接映射到 `oc` 命令。 更为常见的情况是，它们等同于你可以执行的 CLI 操作类型。 

例如，具有 `list` 谓词表示你可以显示资源名称的所有对象的列表 (`oc get`)。 `get` 谓词表示，如果你知道某个特定对象的名称，则可以显示该对象的详细信息 (`oc describe`)。

## <a name="configure-the-customer-administrator-role"></a>配置客户管理员角色

只能在创建群集期间通过提供 `--customer-admin-group-id` 标志来配置“客户-管理员-群集”群集角色。 目前无法在 Azure 门户中配置此字段。 若要了解如何配置 Azure Active Directory 和管理员组，请参阅 [Azure Red Hat OpenShift 的 Azure Active Directory 集成](howto-aad-app-configuration.md)。

## <a name="confirm-membership-in-the-customer-administrator-role"></a>确认客户管理员角色中的成员身份

若要确认客户管理员组中的成员身份，请尝试 OpenShift CLI 命令 `oc get nodes` 或 `oc projects`。 如果你有“客户-管理员-群集”角色，`oc get nodes` 会显示节点的列表；如果你只有“客户-管理员-项目”角色，该命令会显示权限错误。 `oc projects` 会显示群集中的所有项目，而不只是你正在处理的项目。

若要进一步探究群集中的角色和权限，可以使用 [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) 命令。

## <a name="next-steps"></a>后续步骤

配置“客户-管理员-群集”群集角色：
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 的 Azure Active Directory 集成](howto-aad-app-configuration.md)
