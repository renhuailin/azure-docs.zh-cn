---
title: Azure Spring Cloud 的服务计划和配额
description: 了解 Azure Spring Cloud 的服务配额和服务计划
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 1bd1ed797ce737260289ab40ed6a16c4ce4581e7
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015587"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure Spring Cloud 的配额和服务计划

本文适用于：✔️ Java ✔️ C#

所有 Azure 服务都设置针对资源和功能的默认限制和配额。   Azure Spring Cloud 提供两个定价层：“基本”和“标准”。 本文将详细介绍这两个层的限制。

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure Spring Cloud 服务层级和限制

| 资源 | 范围 | 基本 | Standard
------- | ------- | -------
vCPU | 每个应用实例 | 1 | 4
内存 | 每个应用实例 | 2 GB | 8 GB
Azure Spring Cloud 服务实例 | 每个区域每个订阅 | 10 | 10
应用实例总数 | 每个 Azure Spring Cloud 服务实例 | 25 | 500
自定义域 | 每个 Azure Spring Cloud 服务实例 | 0 | 25
永久性卷 | 每个 Azure Spring Cloud 服务实例 | 1 GB/应用 x 10 个应用 | 50 GB/应用 x 10 个应用
入站公共终结点 | 每个 Azure Spring Cloud 服务实例| 10 <sup>1</sup> | 10 <sup>1</sup>
出站公共 IP | 每个 Azure Spring Cloud 服务实例| 1 <sup>2</sup> | 2 <sup>2</sup> <br> 如果使用 VNet<sup>2</sup>，则为 1

<sup>1</sup> 可以通过支持请求将此限制增加到每个应用最多 1 个。

<sup>2</sup> 可以通过支持请求将此限制增加到最多 10 个。

> [!TIP]
> 为每个服务实例列出的应用实例总数限制适用于处于任何状态（包括已停止状态）的应用和部署。 请删除未在使用的应用或部署。

## <a name="next-steps"></a>后续步骤

某些默认限制可以提高。 如果你的设置要求提高默认限制，请[创建支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。
