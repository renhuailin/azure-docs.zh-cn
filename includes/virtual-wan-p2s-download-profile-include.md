---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628890"
---
1. 在虚拟 WAN 的页面上，选择“用户 VPN 配置”。
1. 在“用户 VPN 配置”页面上，选择一个配置，然后选择“下载虚拟 WAN 用户 VPN 配置文件” 。 下载 WAN 级配置时，你将获得内置的基于流量管理器的用户 VPN 配置文件。 有关全局配置文件或基于中心的配置文件的详细信息，请参阅[中心配置文件](../articles/virtual-wan/global-hub-profile.md)。 使用全局配置文件可简化故障转移方案。

   
   如果中心由于某种原因而不可用，则该服务提供的内置流量管理可确保（通过不同的中心）连接到点到站点用户的 Azure 资源。 始终可以通过导航到中心来下载特定于中心的 VPN 配置。 在“用户 VPN (点到站点)”下，下载虚拟中心用户 VPN 配置文件 。
1. 在“下载虚拟 WAN 用户 VPN 配置文件”页面上，选择“身份验证类型”，然后选择“生成和下载配置文件”  。 这会生成配置文件包，还会下载包含配置设置的 zip 文件。
