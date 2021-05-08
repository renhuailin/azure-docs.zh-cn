---
title: Windows 虚拟桌面（经典版）的数据位置 - Azure
description: Windows 虚拟桌面（经典版）数据和元数据存储位置的简要概述。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7726795582ffa3d85601ec123b58ab705442ef67
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444983"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Windows 虚拟桌面（经典版）的数据位置

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。 要尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[本文](../data-locations.md)。

Windows 虚拟桌面目前可用于所有地理位置。 最初，只能将服务元数据存储在美国 (US) 境内。 管理员可以在创建主机池虚拟机和相关服务（如文件服务器）时选择存储用户数据的位置。 请查看 [Azure 数据中心地图](https://azuredatacentermap.azurewebsites.net/)详细了解 Azure 地理位置。

>[!NOTE]
>Microsoft 不会控制或限制你或你的用户访问你的用户和特定于应用程序的数据的区域。

>[!IMPORTANT]
>Windows 虚拟桌面将租户名称、主机池名称、应用程序组名称和用户主体名称等全局元数据信息存储在美国中的数据中心。 存储的元数据静态加密，并且异地冗余镜像在美国境内维护。 所有客户数据（如应用程序设置和用户数据）将储存在客户选择的位置，并且不受服务管理。

出于灾难恢复目的，在美国境内复制服务元数据。