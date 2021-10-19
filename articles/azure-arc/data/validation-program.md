---
title: 已启用 Azure Arc 的数据服务验证
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/30/2021
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
description: 介绍适用于已启用 Azure Arc 的数据服务的 Kubernetes 发行版验证程序。
keywords: Kubernetes、Arc、Azure、K8s、验证、数据服务、SQL 托管实例
ms.openlocfilehash: 45c6967a536bc4e32e2382acd795f508a4bea0a4
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615175"
---
# <a name="azure-arc-enabled-data-services-kubernetes-validation"></a>已启用 Azure Arc 的数据服务 Kubernetes 验证

已启用 Azure Arc 的数据服务团队与行业合作伙伴协作，验证特定发行版和解决方案，以托管已启用 Azure Arc 的数据服务。 该验证为数据服务扩展了[已启用 Azure Arc 的 Kubernetes 验证](../kubernetes/validation-program.md)。 本文介绍经验证支持数据服务的合作伙伴解决方案、版本、Kubernetes 版本、SQL 引擎版本和超大规模 PostgreSQL 版本。 

若要了解如何验证已启用 Azure Arc 的所有组件，请参阅[验证程序概述](../validation-program/overview.md)

> [!NOTE]
> 目前，已启用 Azure Arc 的 SQL 托管实例在选定区域普遍可用。
>
> 已启用 Azure Arc 的超大规模 PostgreSQL 在选定区域提供预览版。

## <a name="partners"></a>合作伙伴

### <a name="cisco"></a>Cisco

|解决方案和版本 | Kubernetes 版本 | 已启用 Azure Arc 的数据服务版本 | SQL 引擎版本 | 超大规模 PostgreSQL 版本
|-----|-----|-----|-----|-----|
|VMware 上的 Cisco Hyperflex <br/> Cisco IKS ESXi 6.7 U3 |v1.19.5|v1.0.0_2021-07-30|15.0.2148.140| 未验证 |

### <a name="dell"></a>Dell

|解决方案和版本 | Kubernetes 版本 | 已启用 Azure Arc 的数据服务版本 | SQL 引擎版本 | 超大规模 PostgreSQL 版本
|-----|-----|-----|-----|-----|
| Dell EMC PowerFlex |1.19.7|v1.0.0_2021-07-30|15.0.2148.140 | 未验证 |
| PowerFlex 版本 3.6 |1.19.7|v1.0.0_2021-07-30|15.0.2148.140 | 未验证 |
| PowerFlex CSI 版本 1.4 |1.19.7|v1.0.0_2021-07-30|15.0.2148.140 | 未验证 |
| PowerStore X|1.20.6|v1.0.0_2021-07-30|15.0.2148.140 |postgres 12.3 (Ubuntu 12.3-1) |
| Powerstore T|1.20.6|v1.0.0_2021-07-30|15.0.2148.140 |postgres 12.3 (Ubuntu 12.3-1)|

### <a name="nutanix"></a>Nutanix

|解决方案和版本 | Kubernetes 版本 | 已启用 Azure Arc 的数据服务版本 | SQL 引擎版本 | 超大规模 PostgreSQL 版本
|-----|-----|-----|-----|-----|
| Karbon 2.2<br/>AOS：5.19.1.5<br/>AHV：20201105.1021<br/>PC：版本 pc.2021.3.02<br/> | 1.19.8-0 | v1.0.0_2021-07-30 | 15.0.2148.140|postgres 12.3 (Ubuntu 12.3-1)|

### <a name="platform-9"></a>Platform 9

|解决方案和版本 | Kubernetes 版本 | 已启用 Azure Arc 的数据服务版本 | SQL 引擎版本 | 超大规模 PostgreSQL 版本
|-----|-----|-----|-----|-----|
| Platform9 托管 Kubernetes v5.3.0 | 1.20.5 | v1.0.0_2021-07-30| 15.0.2148.140 | 未验证 |

### <a name="purestorage"></a>PureStorage

|解决方案和版本 | Kubernetes 版本 | 已启用 Azure Arc 的数据服务版本 | SQL 引擎版本 | 超大规模 PostgreSQL 版本
|-----|-----|-----|-----|-----|
| Portworx Enterprise 2.7 | 1.20.7 | v1.0.0_2021-07-30 | 15.0.2148.140 | 未验证 |

### <a name="red-hat"></a>Red Hat

|解决方案和版本 | Kubernetes 版本 | 已启用 Azure Arc 的数据服务版本 | SQL 引擎版本 | 超大规模 PostgreSQL 版本
|-----|-----|-----|-----|-----|
| OpenShift 7.13 | 1.20.0 | v1.0.0_2021-07-30 | 15.0.2148.140 | postgres 12.3 (Ubuntu 12.3-1)|

### <a name="vmware"></a>VMware

|解决方案和版本 | Kubernetes 版本 | 已启用 Azure Arc 的数据服务版本 | SQL 引擎版本 | 超大规模 PostgreSQL 版本
|-----|-----|-----|-----|-----|
| TKGm v1.3.1 | 1.20.5 | v1.0.0_2021-07-30 | 15.0.2148.140|postgres 12.3 (Ubuntu 12.3-1)|

## <a name="data-services-validation-process"></a>数据服务验证过程

已启用 Azure Arc 的 Sonobuoy 数据服务插件在 Kubernetes 群集上自动预配和测试已启用 Azure Arc 的数据服务。

### <a name="prerequisites"></a>先决条件

安装工具： 

- [Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata)
- [kubectl](https://kubernetes.io/docs/home/)
- [Azure Data Studio - 预览体验版本](https://github.com/microsoft/azuredatastudio)

创建配置为访问目标 Kubernetes 群集的 Kubernetes 配置文件，并设置为当前上下文。 该文件的生成方式和从本地引入计算机的方式因平台而异。 请参阅 [Kubernetes.io](https://kubernetes.io/docs/home/)。

### <a name="process"></a>过程

一致性测试作为已启用 Azure Arc 的数据服务的一部分运行。 运行这些测试的先决条件是，为使用中的 Kubernetes 发行版传递已启用 Azure Arc 的 Kubernetes 测试。

这些测试验证产品是否符合运行和操作数据服务的要求。 这一过程有助于评估产品是否已准备好供 Enterprise 部署。

在间接连接模式下，数据服务测试涵盖以下内容

1. 在间接模式下部署数据控制器
2. 部署[已启用 Azure Arc 的 SQL 托管实例](create-sql-managed-instance.md)
3. 部署[已启用 Azure Arc 的超大规模 PostgreSQL](create-postgresql-hyperscale-server-group.md)
4. 横向扩展[超大规模 PostgreSQL ](scale-out-in-postgresql-hyperscale-server-group.md)

在已启用 Azure Arc 的数据服务的未来版本中，将添加更多测试。

## <a name="additional-information"></a>其他信息

- [验证计划概述](../validation-program/overview.md)
- [已启用 Azure Arc 的 Kubernetes 验证](../kubernetes/validation-program.md)
- [Azure Arc 验证计划 - GitHub 项目](https://github.com/Azure/azure-arc-validation/)

## <a name="next-steps"></a>后续步骤

[创建数据控制器](create-data-controller.md)
