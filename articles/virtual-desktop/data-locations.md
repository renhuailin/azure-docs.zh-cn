---
title: Windows 虚拟桌面的数据位置 - Azure
description: Windows 虚拟桌面数据和元数据存储位置的简要概述。
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12ec71a86a5df5954c14097e6a0ec5c8a5138fc5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652422"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Windows 虚拟桌面的数据和元数据的位置

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/data-locations-2019.md)。

Windows 虚拟桌面目前可用于所有地理位置。 管理员可以在创建主机池虚拟机和相关服务（如文件服务器）时选择存储用户数据的位置。 请查看 [Azure 数据中心地图](https://azuredatacentermap.azurewebsites.net/)详细了解 Azure 地理位置。

>[!NOTE]
>Microsoft 不会控制或限制你或你的用户访问你的用户和特定于应用程序的数据的区域。

>[!IMPORTANT]
>Windows 虚拟桌面将全局元数据信息（如租户名称、主机池名称、应用组名称和用户主体名称）存储在数据中心。 每当客户创建服务对象时，他们都必须输入服务对象的位置。 他们输入的位置决定对象元数据的存储位置。 客户会选择某个 Azure 区域，并将元数据存储在相关的地理区域中。 有关所有 Azure 区域和相关地理区域的列表，请参阅 [Azure 地理区域](https://azure.microsoft.com/global-infrastructure/geographies/)。

我们目前支持在以下地区存储元数据：

- 美国 (US)（正式发布）
- 欧洲 (EU)（公共预览版） 

>[!NOTE]
> 选择要在其中创建 Windows 虚拟桌面服务对象的区域时，将看到美国和欧盟地理区域下的区域。 为确保你了解最适合你的部署的区域，请查看 [Azure 全球基础结构地图](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)。

存储的元数据静态加密，并且异地冗余镜像在该地理区域维护。 所有客户数据（如应用设置和用户数据）将储存在客户选择的位置，并且不受服务管理。 随着服务的增长，更多的地区将变得可用。

为了进行灾难恢复，服务元数据是在 Azure 地理区域内复制的。