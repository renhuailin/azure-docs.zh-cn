---
title: Azure Spring Cloud 的服务计划和配额
description: 了解 Azure Spring Cloud 的服务配额和服务计划
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 20ebeb23fe09ba4fd70a724828afadfaa3901abd
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095668"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure Spring Cloud 的配额和服务计划

本文适用于：✔️ Java ✔️ C#

所有 Azure 服务都设置针对资源和功能的默认限制和配额。   Azure Spring Cloud 提供两个定价层：“基本”和“标准”。 本文将详细介绍这两个层的限制。

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure Spring Cloud 服务层级和限制

| 资源 | 范围 | 基本 | Standard
------- | ------- | -------
vCPU | 每个应用实例 | 1 | 4
内存 | 每个应用实例 | 2 GB | 8 GB
Azure 春季云服务实例 | 每个订阅的每个区域 | 10 | 10
应用程序实例总数 | 每个 Azure 春季云服务实例 | 25 | 500
自定义域 | 每个 Azure 春季云服务实例 | 0 | 25 
永久性卷 | 每个 Azure 春季云服务实例 | 1 GB/应用 x 10 个应用 | 50 GB/应用 x 10 个应用

> [!TIP]
> 列出的每个服务实例的应用程序实例总数适用于处于停止状态的应用/部署。 请删除未在使用的应用/部署。

## <a name="next-steps"></a>后续步骤

某些默认限制可以提高。 如果你的设置要求提高默认限制，请[创建支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。
