---
title: 已启用 Azure Arc 的 SQL 托管实例服务层级
description: 介绍可用于已启用 Azure Arc 的 SQL 托管实例部署的服务层级。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: f0196d2a763b27c43fedb6371668321460f09c6f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121782052"
---
# <a name="azure-arc-enabled-sql-managed-instance-service-tiers"></a>已启用 Azure Arc 的 SQL 托管实例服务层级

作为 Azure SQL 产品系列的一部分，启用了 Azure Arc 的 SQL 托管实例在两个 [vCore](../../azure-sql/database/service-tiers-vcore.md) 服务层级中提供。

- 常规用途是一种预算较低的层级，旨在用于具有常见性能和可用性功能的大多数工作负载。
- 业务关键层级旨在用于具有更高可用性功能的性能敏感工作负载。

当前，业务关键服务层级为[预览状态版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 常规用途服务层级已正式发布。 

在 Azure 中，存储和计算由 Microsoft 提供，每个服务层级的性能、吞吐量、可用性等方面都有受保证的服务级别协议 (SLA)。 借助已启用 Azure Arc 的数据服务，客户可提供存储和计算。 因而，对于已启用 Azure Arc 的数据服务，不向客户提供受保证的 SLA。 不过，客户可灵活地引入其自己的高性能硬件，而不考虑服务层级。 

## <a name="service-tier-comparison"></a>服务层级比较

下面介绍了在两个服务层级中已启用 Azure Arc 的数据服务提供的各种功能：


区域 | 业务关键（预览版）* | 常规用途
----------|-----------------|------------------
特征集 | 与 SQL Server Enterprise Edition 相同 | 与 SQL Server Standard Edition 相同
CPU 限制/实例 | 无限制  | 24 个核心
内存限制/实例 | 无限制 | 128 GB
高可用性 | 可用性组 | 单实例，具有 Kubernetes 重新部署 + 共享存储。
读取横向扩展 | 可用性组 | 无
价格 IP 部分的 AHB 汇率 | 1:1 Enterprise Edition <br> 4:1 Standard Edition | 1:4 Enterprise Edition <br> 1:1 Standard Edition 
开发/测试定价 | 免费 | 免费

\* 目前，业务关键服务层级处于预览版状态，在此预览版期间，使用时不会产生任何费用。 当我们更接近正式发布时，某些功能可能会更改。

## <a name="how-to-choose-between-the-service-tiers"></a>如何在服务层级之间进行选择

由于客户会根据业务需求引入自己的满足性能和可用性要求的硬件，因此服务层级之间的主要区别在于软件级别提供的内容。 

### <a name="choose-general-purpose-if"></a>在以下情况选择常规用途

- CPU/内存要求满足或处于常规用途服务层级的限制范围内
- Kubernetes 提供的高可用性选项（如 pod 重新）足以应对工作负载
- 应用程序不需要读取横向扩展
- 应用程序不需要业务关键服务层级中的任何功能（与 SQL Server Enterprise Edition 相同）

### <a name="choose-business-critical-if"></a>在以下情况选择业务关键

- CPU/内存要求超出常规用途服务层级的限制范围内
- 与 Kubernetes 提供的高可用性相比，应用程序需要更高级别的高可用性（如内置可用性组）来处理应用程序故障转移。 
- 应用程序可以利用读取横向扩展将读取工作负载分流到次要副本
- 应用程序需要仅在业务关键服务层级中提供的功能（与 SQL Server Enterprise Edition 相同）
