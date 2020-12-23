---
title: 在 Azure Red Hat OpenShift 中管理安全上下文约束 |Microsoft Docs
description: Azure Red Hat OpenShift 群集管理员的安全上下文约束
services: container-service
author: troy0820
ms.author: b-trconn
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: eb5c568f056a99187a0e7a78a6f89b206f2d8dec
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220238"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>在 Azure Red Hat OpenShift 中管理安全上下文约束

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将在年6月 30 2022 日停用。 支持创建新的 Azure Red Hat OpenShift 3.11 群集持续到30年 11 2020 月30日。 停用后，剩余的 Azure Red Hat OpenShift 3.11 群集将关闭，以防出现安全漏洞。
> 
> 按照本指南 [创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。
> 如果有特定问题， [请](mailto:arofeedback@microsoft.com)联系我们。

 (Scc 的安全上下文约束) 允许群集管理员控制 pod 的权限。 若要了解有关此 API 类型的详细信息，请参阅 [scc 的体系结构文档](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html)。 你可以通过使用 CLI，将实例中的 Scc 作为常规 API 对象进行管理。

## <a name="list-security-context-constraints"></a>列出安全上下文约束

若要获取 Scc 的当前列表，请使用此命令： 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>检查对象的安全上下文约束

若要检查特定 SCC，请使用 `oc get` 、 `oc describe` 或 `oc edit` 。  例如，若要检查 **受限制** 的 SCC，请使用此命令：
```bash
$ oc describe scc restricted
Name:                    restricted
Priority:                <none>
Access:
  Users:                <none>
  Groups:                system:authenticated
Settings:
  Allow Privileged:            false
  Default Add Capabilities:        <none>
  Required Drop Capabilities:        KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:            <none>
  Allowed Seccomp Profiles:        <none>
  Allowed Volume Types:            configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:            false
  Allow Host Ports:            false
  Allow Host PID:            false
  Allow Host IPC:            false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:            <none>
    UID Range Max:            <none>
  SELinux Context Strategy: MustRunAs
    User:                <none>
    Role:                <none>
    Type:                <none>
    Level:                <none>
  FSGroup Strategy: MustRunAs
    Ranges:                <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:                <none>
```
## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md) 
