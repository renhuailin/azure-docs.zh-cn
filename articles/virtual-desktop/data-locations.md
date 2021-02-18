---
title: Windows 虚拟桌面的数据位置-Azure
description: 对 Windows 虚拟机的数据和元数据的存储位置的简要概述。
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12ec71a86a5df5954c14097e6a0ec5c8a5138fc5
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652422"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Windows 虚拟桌面的数据和元数据位置

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/data-locations-2019.md)。

Windows 虚拟桌面目前可用于所有地理位置。 管理员可以在创建主机池虚拟机和相关服务（如文件服务器）时选择存储用户数据的位置。 在 [azure 数据中心地图](https://azuredatacentermap.azurewebsites.net/)上详细了解 azure 地理位置。

>[!NOTE]
>Microsoft 不会控制或限制你或你的用户可以访问你的用户和特定于应用程序的数据的区域。

>[!IMPORTANT]
>Windows 虚拟桌面存储数据中心内的全局元数据信息，如租户名称、主机池名称、应用组名称和用户主体名称。 每次客户创建服务对象时，都必须输入服务对象的位置。 它们所输入的位置确定将存储对象的元数据的位置。 客户将选择一个 Azure 区域，并且元数据将存储在相关地理位置。 有关所有 Azure 区域和相关地理位置的列表，请参阅 [azure 地理](https://azure.microsoft.com/global-infrastructure/geographies/)位置。

我们目前支持在以下地理位置存储元数据：

- 美国 (我们)  (公开发布) 
- 欧洲 (EU)  (公共预览版)  

>[!NOTE]
> 在中选择要在其中创建 Windows 虚拟桌面服务对象的区域时，你将在美国和欧洲地区看到区域。 为了确保了解最适合你的部署的区域，请查看 [我们的 Azure 全球基础结构地图](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)。

存储的元数据是静态加密的，异地冗余镜像将保留在地理位置。 所有客户数据（如应用设置和用户数据）驻留在客户选择的位置，并且不受服务管理。 随着服务的增长，更多的地理位置将变为可用。

出于灾难恢复目的，在 Azure 地域内复制服务元数据。