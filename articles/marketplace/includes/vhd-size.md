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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92283686"
---
如果选择了一个已预配置操作系统（以及可选的其他服务）的 VM，则已经选取了一种标准 Azure VM 大小。 使用预先配置的 OS 启动解决方案是建议的方法。 但是，若要手动安装 OS，则必须在 VM 映像中调整主要 VHD 的大小。 确保 OS 磁盘大小在 Linux 或 Windows 的限制范围内。

| OS | VHD 大小 |
| --- | --- |
| Linux | 30-1023 GB |
| Windows | 30-250 GB |
|

作为已批准的基础映像提供的 Windows 或 SQL Server 基础映像已满足这些要求；请不要更改 VHD 的格式或大小。

数据磁盘可以大至 1TB。 决定其大小时，请记住，客户无法在部署时对映像中的 VHD 调整大小。 将数据磁盘 VHD 创建为固定格式 VHD。 它们还应可扩展（稀疏/动态）。 数据磁盘最初可以是空的或包含数据。