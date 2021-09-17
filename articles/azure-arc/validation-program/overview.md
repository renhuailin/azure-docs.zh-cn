---
title: 已启用 Azure Arc 的服务验证概述
description: 说明 Azure Arc 验证过程，以符合已启用 Arc 的 Kubernetes、数据服务和群集扩展。
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 1cf208b9a476f4a2a16df157ebfa0bb7661cfc1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783358"
---
# <a name="overview-of-azure-arc-enabled-service-validation"></a>已启用 Azure Arc 的服务验证概述

Microsoft 建议在经过验证的平台上运行已启用 Azure Arc 的服务。 本文介绍如何验证已启用 Azure Arc 的各种组件。 

目前，经过验证的解决方案可从 Kubernetes 和数据服务的合作伙伴处获得。

## <a name="kubernetes"></a>Kubernetes

已启用 Azure Arc 的 Kubernetes 适用于任何经云原生计算基础 (CNCF) 认证的 Kubernetes 群集。 Azure Arc 团队与关键行业 Kubernetes 产品/服务提供商合作，通过其 [Kubernetes 分发](../kubernetes/validation-program.md)来验证已启用 Azure Arc 的 Kubernetes。 将验证这些提供商发布的 Kubernetes 分发的未来主要和次要版本是否兼容已启用 Azure Arc 的 Kubernetes。

## <a name="data-services"></a>数据服务

我们还与原始设备制造商 (OEM) 合作伙伴和存储提供商合作，以验证[已启用 Azure Arc 的数据服务](../data/validation-program.md)解决方案。

## <a name="validation-process"></a>验证过程

GitHub 中提供了 Azure Arc 验证过程。 若要详细了解如何验证使用 Azure Arc 的产品/服务、测试工具以及策略，请参阅 GitHub 中的 [Azure Arc 验证过程](https://github.com/Azure/azure-arc-validation/)。

## <a name="next-steps"></a>后续步骤

* [经验证的 Kubernetes 分发](../kubernetes/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)

* [数据服务的已验证 Kubernetes 分发](../data/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)
