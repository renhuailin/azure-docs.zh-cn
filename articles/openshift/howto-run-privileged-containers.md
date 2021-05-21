---
title: 在 Azure Red Hat OpenShift 群集中运行特权容器 | Microsoft Docs
description: 运行特权容器以监视安全性和符合性。
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, aquasec, twistlock, red hat
ms.openlocfilehash: 77e75232b7f9d23f1d07931cc7dc231174e1312d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633556"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>在 Azure Red Hat OpenShift 群集中运行特权容器

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将于 2022 年 6 月 30 日停用。 对新建 Azure Red Hat OpenShift 3.11 群集的支持会持续到 2020 年 11 月 30 日。 在停用之后，剩余的 Azure Red Hat OpenShift 3.11 群集将会关闭，以防出现安全漏洞。
> 
> 请按照本指南[创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。
> 如有具体问题，[请与我们联系](mailto:arofeedback@microsoft.com)。

不能在 Azure Red Hat OpenShift 群集上运行任意特权容器。
有两个允许在 ARO 群集上运行的安全性监视和符合性解决方案。
本文档介绍与安全产品供应商的通用 OpenShift 部署文档的不同之处。


在按照供应商的说明操作之前，请通读这些说明。
下面特定于产品的步骤中的节标题直接引用供应商文档中的节标题。

## <a name="before-you-begin"></a>开始之前

大多数安全产品文档假定你具有群集管理员权限。
客户管理员不具有 Azure Red Hat OpenShift 的全部特权。 对修改群集范围的资源所需的权限设置了限制。

首先，通过运行 `oc get scc`，确保用户以客户管理员身份登录到群集。 身份为客户管理组成员的所有用户有权查看对群集的安全上下文约束 (SCC)。

接下来，确保 `oc` 二进制版本为 `3.11.154`。
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Aqua Security 特定于产品的步骤
可在 [Aqua Security 部署文档](https://docs.aquasec.com/docs/openshift-red-hat)中查看即将修改的基本说明。 此处的步骤基于 Aqua 部署文档。

第一步是对要更新的所需 SCC 进行批注。 这些批注可阻止群集的同步 Pod 还原对这些 SCS 的任何更改。

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>步骤 1：满足先决条件
请记得使用 ARO 客户管理员角色而不是群集管理员角色登录到群集。

创建项目和服务帐户。
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

可以通过以下命令将“客户-管理员-群集”角色分配给 aqua 帐户，而不分配群集读取者角色。
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

继续按照步骤 1 中的后续说明进行操作。  这些说明描述了如何设置 Aqua 注册表的机密。

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>步骤 2：部署 Aqua 服务器、数据库和网关
按照 Aqua 文档中提供的 aqua-console.yaml 安装步骤操作。

修改提供的 `aqua-console.yaml`。  删除带标记的前两个对象 `kind: ClusterRole` 和 `kind: ClusterRoleBinding`。  由于客户管理员此时无权修改 `ClusterRole` 和 `ClusterRoleBinding` 对象，因此不会创建这些资源。

第二个要修改的是 `aqua-console.yaml` 的 `kind: Route` 部分。 在 `aqua-console.yaml` 文件中替换 `kind: Route` 对象的以下 yaml。
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

按照后续说明进行操作。

### <a name="step-3-login-to-the-aqua-server"></a>步骤 3：登录 Aqua 服务器
此部分不会以任何方式进行修改。  按照 Aqua 文档进行操作。

使用以下命令获取 Aqua 控制台地址。
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>步骤 4：部署 Aqua 执行器
部署执行器时应设置以下字段：

| 字段          | 值         |
| -------------- | ------------- |
| 业务流程协调程序   | OpenShift     |
| ServiceAccount | aqua-account  |
| Project        | aqua-security |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Prisma Cloud/Twistlock 的特定于产品的步骤

可在 [Prisma Cloud 部署文档](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)中查看要修改的基本说明

首先安装 `twistcli` 工具，按照“安装 Prisma Cloud”和“下载 Prisma Cloud 软件”部分中所述操作。

创建新的 OpenShift 项目
```
oc new-project twistlock
```

跳过可选部分“将 Prisma Cloud 映像推送到专用注册表”。 这在 Azure Red Hat OpenShift 上不起作用。 改用联机注册。

应用下述更正时，可以按照官方文档中的说明操作。
从“安装控制台”部分开始。

### <a name="install-console"></a>安装控制台

在步骤 2 中的 `oc create -f twistlock_console.yaml` 期间，在创建命名空间时会收到错误。
可以放心将其忽略，之前已使用 `oc new-project` 命令创建命名空间。

为存储类型使用 `azure-disk`。

### <a name="create-an-external-route-to-console"></a>创建指向控制台的外部路由

如果希望使用 oc 命令，可以按照文档或下面的说明操作。
将以下路由定义复制到计算机上名为 twistlock_route.yaml 的文件中
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
然后，运行以下命令：
```
oc create -f twistlock_route.yaml
```

可以使用以下命令获取分配给 Twistlock 控制台的 URL：`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>配置控制台

按照 Twistlock 文档中的说明操作。

### <a name="install-defender"></a>安装 Defender

在步骤 2 中的 `oc create -f defender.yaml` 期间，当创建群集角色和群集角色绑定时会收到错误。
你可以忽略这些异常。

将仅在计算节点上部署 Defender。 无需使用节点选择器来限制节点。
