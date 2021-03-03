---
title: 虚拟网络中的 Azure 春季 Cloud 疑难解答
description: Azure 春季云虚拟网络故障排除指南。
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b2369a6380c7b74302d32366d0604fca616fc3ed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698223"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>在虚拟网络中排除 Azure Spring Cloud 的故障

本文档将帮助你解决在虚拟网络中使用 Azure 春季云时可能出现的各种问题。

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>我在创建 Azure Spring Cloud 服务实例时遇到了问题

若要创建 Azure 春季云的实例，你必须有足够的权限将实例部署到虚拟网络。  "春季云服务实例" 本身必须 [为虚拟网络授予 Azure "春季云服务" 权限](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)。

如果使用 Azure 门户设置 Azure 春季云服务实例，则 Azure 门户将验证权限。

若要使用 [Azure CLI](/cli/azure/get-started-with-azure-cli)设置 Azure 春季云服务实例，请验证：

- 订阅处于活动状态。
- 位置受 Azure Spring Cloud 的支持。
- 已创建实例的资源组。
- 资源名称符合命名规则。 该名称只能包含小写字母、数字和连字符。 第一个字符必须是字母。 最后一个字符必须是字母或数字。 该值必须包含 2 到 32 个字符。

若要使用资源管理器模板设置 Azure 春季云服务实例，请参阅 [了解 Azure 资源管理器模板的结构和语法](../azure-resource-manager/templates/template-syntax.md)。

### <a name="common-creation-issues"></a>常见的创建问题

| 错误消息 | 如何解决 |
|------|------|
| 策略不允许 Azure 春季创建的资源。 | 在你自己的虚拟网络中部署 Azure 春季云时将创建网络资源。 请检查是否定义了 [Azure 策略](../governance/policy/overview.md) 来阻止这些创建。 无法在错误消息中找到资源。 |
| 提供的子网与路由表关联，请将它们解除关联。 | 当前不支持在与现有路由表关联的子网中部署 Azure 春季 Cloud，请取消关联，然后重试。 |
| 不 allowlisted 所需的流量。 | 请参阅 [客户在 VNET 中运行 Azure 春季 Cloud 的责任](spring-cloud-vnet-customer-responsibilities.md) ，以确保所需的流量 allowlisted。 |

## <a name="my-application-cant-be-registered"></a>我的应用程序无法注册

如果你的虚拟网络是通过自定义 DNS 设置配置的，则会出现此问题。 在这种情况下，Azure 春季 Cloud 使用的专用 DNS 区域无效。 在自定义 DNS 服务器中将 Azure DNS IP 168.63.129.16 添加为上游 DNS 服务器。

## <a name="other-issues"></a>其他问题

[排查常见的 Azure 春季云问题](./spring-cloud-troubleshoot.md)。