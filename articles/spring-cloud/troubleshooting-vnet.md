---
title: 在虚拟网络中排除 Azure Spring Cloud 的故障
description: Azure Spring Cloud 虚拟网络故障排除指南。
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 95b288181180e2409bcb18f4f8233af568bd311c
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015439"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>在虚拟网络中排除 Azure Spring Cloud 的故障

本文档将帮助你解决在虚拟网络中使用 Azure Spring Cloud 时可能出现的各种问题。

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>我在创建 Azure Spring Cloud 服务实例时遇到了问题

若要创建 Azure Spring Cloud 实例，你必须具有将该实例部署到虚拟网络的足够权限。  Spring Cloud 服务实例必须自行[为 Azure Spring Cloud 服务授予对虚拟网络的权限](./how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)。

如果使用 Azure 门户设置 Azure Spring Cloud 服务实例，则 Azure 门户会验证权限。

若要使用 [Azure CLI](/cli/azure/get-started-with-azure-cli) 设置 Azure Spring Cloud 服务实例，请确认：

- 订阅处于活动状态。
- 位置受 Azure Spring Cloud 的支持。
- 已创建实例的资源组。
- 资源名称符合命名规则。 该名称只能包含小写字母、数字和连字符。 第一个字符必须是字母。 最后一个字符必须是字母或数字。 该值必须包含 2 到 32 个字符。

若要使用资源管理器模板设置 Azure Spring Cloud 服务实例，请参阅[了解 Azure 资源管理器模板的结构和语法](../azure-resource-manager/templates/syntax.md)。

### <a name="common-creation-issues"></a>常见创建问题

| 错误消息 | 如何解决 |
|------|------|
| 策略不允许 Azure Spring Cloud 创建的资源。 | 在你自己的虚拟网络中部署 Azure Spring Cloud 时，会创建网络资源。 请检查是否已定义 [Azure Policy](../governance/policy/overview.md) 来阻止这些创建操作。 在错误消息中可以找到无法创建的资源。 |
| 所需的流量未加入允许列表。 | 请参阅[在 VNET 中运行 Azure Spring Cloud 的客户职责](./vnet-customer-responsibilities.md)，以确保将所需的流量加入允许列表。 |

## <a name="my-application-cant-be-registered"></a>我的应用程序无法注册

如果虚拟网络已配置了自定义 DNS 设置，则会出现此问题。 在这种情况下，Azure Spring Cloud 使用的专用 DNS 区域无效。 请在自定义 DNS 服务器中将 Azure DNS IP 168.63.129.16 作为上游 DNS 服务器进行添加。

## <a name="other-issues"></a>其他问题

[排查常见的 Azure Spring Cloud 问题](./troubleshoot.md)
