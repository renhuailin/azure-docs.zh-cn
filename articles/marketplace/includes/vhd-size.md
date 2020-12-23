---
title: include 文件
description: 文件
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283686"
---
如果选择了一个预配置了操作系统 (和可选的附加服务) 的 Vm，则已选择了标准 Azure VM 大小。 使用预先配置的 OS 启动解决方案是建议的方法。 但是，如果手动安装操作系统，则必须在 VM 映像中调整主要 VHD 的大小。 确保操作系统磁盘大小在 Linux 或 Windows 的限制范围内。

| OS | VHD 大小 |
| --- | --- |
| Linux | 30到 1023 GB |
| Windows | 30到 250 GB |
|

作为已批准基准提供的基本 Windows 或 SQL Server 映像已满足这些要求，因此请勿更改 VHD 的格式或大小。

数据磁盘可以大至 1TB。 决定其大小时，请记住，客户无法在部署时对映像中的 VHD 调整大小。 将数据磁盘 Vhd 创建为固定格式 Vhd。 它们还应 (稀疏/动态) 展开。 数据磁盘最初可以是空的或包含数据。