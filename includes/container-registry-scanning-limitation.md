---
title: include 文件
description: include 文件
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/19/2021
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 83a42f7a68dc34770106e258b4ed5949feb48c8d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471453"
---
> [!IMPORTANT]
> 某些功能可能不可用，或者需要在容器注册表中进行更多配置，以限制对专用终结点、选定子网或 IP 地址的访问。 
> * 禁用对注册表的公共网络访问后，某些[受信任服务](../articles/container-registry/allow-access-trusted-services.md)（包括 Azure 安全中心）对注册表的访问需要启用一个可绕过网络规则的网络设置。
> * 某些 Azure 服务的实例（包括 Azure DevOps 服务和 Azure 容器实例）当前无法访问容器注册表。
> * 如果注册表具有已批准的专用终结点且禁用了公共网络访问，则无法使用 Azure 门户、Azure CLI 或其他工具在虚拟网络外部列出存储库和标记。
