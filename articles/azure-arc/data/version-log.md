---
title: 已启用 Azure Arc 的数据服务 - 发布版本
description: 已启用 Azure Arc 的数据服务的版本（按发布日期列出）的日志
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 08/06/2021
ms.topic: conceptual
ms.openlocfilehash: 3e636fe5ff6477f78f09d940b70099b8cbf41fb9
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862798"
---
# <a name="version-log"></a>版本日志

下表介绍了随时间变化的各种版本：

|Date|版本名称|容器映像标记|CRD 前缀和版本|ARM API 版本|`arcdata` Azure CLI 扩展版本|已启用 Arc 的 Kubernetes Helm chart 扩展版本|Azure Data Studio 的 Arc 数据扩展|
|---|---|---|---|---|---|---|---|
|2021 年 7 月 30 日|已启用 Arc 的 SQL 托管实例常规用途版和已启用 Arc 的 SQL Server 正式发布版|v1.0.0_2021-07-30|`datacontrollers`：v1beta1、v1 <br/>`exporttasks.tasks`：v1beta1、v1 <br/>`monitors`：v1beta1、v1 <br/>`sqlmanagedinstances.sql`：v1beta1、v1 <br/>`postgresqls`：v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql`：v1beta1 <br/>`dags.sql`：v1beta1 <br/>|2021-08-01（稳定版）|1.0|1.0.16701001，版本序列：稳定版|0.9.5|
|2021 年 8 月 3 日|更新到 Azure Data Studio 的 Azure Arc 扩展以与 7 月 30 日的正式发布版保持一致|没有变化|没有变化|没有变化|没有变化|没有变化|0.9.6|

对于完整 CRD，请将 `.arcdata.microsoft.com` 追加到前缀。 
