---
title: 在 Azure Red Hat OpenShift 中管理资源| Microsoft Docs
description: 在 Azure Red Hat OpenShift 群集中管理项目、模板、映像流
services: openshift
keywords: red hat openshift 项目请求自配置程序
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: azure-redhat-openshift
ms.openlocfilehash: bf2cf5a0d41af15821035c615fe071c8580e125f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633098"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>在 Azure Red Hat OpenShift 群集中管理项目、模板、映像流

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将于 2022 年 6 月 30 日停用。 对新建 Azure Red Hat OpenShift 3.11 群集这一功能的支持会持续到 2020 年 11 月 30 日。 在停用之后，剩余的 Azure Red Hat OpenShift 3.11 群集将会关闭，以防出现安全漏洞。
> 
> 请按照本指南[创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。
> 如有具体问题，[请与我们联系](mailto:arofeedback@microsoft.com)。

在 OpenShift 容器平台中，项目用于对相关对象进行分组和隔离。 作为管理员，你可以向开发人员提供对特定项目的访问权限，允许他们创建自己的项目，并向他们授予对各个项目的管理权限。

## <a name="self-provisioning-projects"></a>自行预配项目

可以让开发人员创建他们自己的项目。 API 终结点负责根据名为“项目请求”的模板预配项目。 当开发人员创建新项目时，Web 控制台和 `oc new-project` 命令使用此终结点。

提交项目请求时，API 会在模板中替换以下参数：

| 参数               | 说明                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | 项目的名称。 必需。             |
| PROJECT_DISPLAYNAME     | 项目的显示名称。 可能为空。 |
| PROJECT_DESCRIPTION     | 项目的说明。 可能为空。  |
| PROJECT_ADMIN_USER      | 管理用户的用户名。       |
| PROJECT_REQUESTING_USER | 请求用户的用户名。           |

使用自配置程序群集角色绑定向开发人员授予对 API 的访问权限。 默认情况下，此功能适用于所有已经过身份验证的开发人员。

## <a name="modify-the-template-for-a-new-project"></a>修改新项目的模板 

1. 以具有 `customer-admin` 权限的用户身份登录。

2. 编辑默认的“项目请求”模板。

   ```
   oc edit template project-request -n openshift
   ```

3. 通过添加以下注释，从 Azure Red Hat OpenShift (ARO) 更新过程中删除默认项目模板：`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   ARO 更新过程不会更新“项目请求”模板。 这使客户能够自定义模板并在群集更新时保留这些自定义项。

## <a name="disable-the-self-provisioning-role"></a>禁用自预配角色

可以阻止经过身份验证的用户组自行预配新项目。

1. 以具有 `customer-admin` 权限的用户身份登录。

2. 编辑自配置程序群集角色绑定。

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. 通过添加以下注释，从 ARO 更新过程中删除该角色：`openshift.io/reconcile-protect: "true"`。

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. 更改群集角色绑定以防止 `system:authenticated:oauth` 创建项目：

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>管理默认模板和 imageStream

在 Azure Red Hat OpenShift 中，可以禁用 `openshift` 命名空间中任何默认模板和映像流的更新。
如果要禁用 `openshift` 命名空间中所有 `Templates` 和 `ImageStreams` 的更新：

1. 以具有 `customer-admin` 权限的用户身份登录。

2. 编辑 `openshift` 命名空间：

   ```
   oc edit namespace openshift
   ```

3. 通过添加以下注释，从 ARO 更新过程中删除 `openshift` 命名空间：`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   可以通过向其添加注释 `openshift.io/reconcile-protect: "true"` 将 `openshift` 命名空间中的各个对象从更新过程中删除。

## <a name="next-steps"></a>后续步骤

试用教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)
