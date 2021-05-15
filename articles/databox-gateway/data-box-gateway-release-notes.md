---
title: Azure Data Box Gateway 正式发布发行说明 | Microsoft Docs
description: 介绍运行正式发布版的 Azure Data Box Gateway 的关键未结问题和解决方案。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 2984f7990b9570c5ec57633de7f7e50162fb6f46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581653"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure Data Box Edge/Azure Data Box Gateway 正式发布发行说明

## <a name="overview"></a>概述

以下发行说明列出了 Azure Data Box Edge 和 Azure Data Box Gateway 正式发布 (GA) 版本的相关重要未结问题和已解决问题。 

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 Data Box Edge/Data Box Gateway 之前，请仔细查看本发行说明中包含的信息。

GA 版本对应于以下软件版本：

- Data Box Gateway 1903 (1.5.814.447)
- Data Box Edge 1903 (1.5.814.447)


## <a name="whats-new"></a>新增功能

- 新的虚拟磁盘映像：现在可在 Azure 门户中获取新的 VHDX 和 VMDK。 下载这些映像来预配、配置和部署新的 Data Box Gateway GA 设备。 在早期预览版本中创建的 Data Box Gateway 设备无法更新到此版本。 有关详细信息，请转到[准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。
- NFS 支持 - NFS 支持目前处于预览阶段，适用于访问 Data Box Edge 和 Data Box Gateway 设备的 v3.0 和 v4.1 客户端。
- 存储复原 -Data Box Edge 设备可以承受一个具有存储复原功能的数据磁盘出现故障。 此功能目前处于预览状态。 可以通过在本地 Web UI 的“存储设置”中选择“弹性”选项来启用存储复原。


## <a name="known-issues-in-ga-release"></a>正式发布版本中的已知问题

下表提供了运行版本的 Data Box Gateway 的已知问题的汇总。

| 不是。 | 功能 | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
| **1.** |文件类型 | 不支持以下文件类型：字符文件、块文件、套接字、管道、符号链接。  |复制这些文件会导致在 NFS 共享上创建 0 长度文件。 这些文件仍处于错误状态，并将在 *error.xml* 中报告。 <br> 指向目录的符号链接导致目录永远不会被标记为脱机。 因此，可能看不到目录上有表明目录处于脱机状态的灰色叉，且所有相关内容都完全上传到 Azure 中。 |
| **2.** |删除 | 由于此版本中的一个 bug，删除 NFS 共享时，可能不会将其删除。 共享状态将显示“正在删除”。  |只有当该共享使用了不受支持的文件名时，才会发生这种情况。 |
| **3.** |复制 | 数据复制失败，并显示以下错误：因文件系统限制而无法完成请求的操作。  |不支持与文件大小超过 128 KB 相关联的备用数据流 (ADS)。   |


## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。
- [准备部署 Azure Data Box Edge](../databox-online/azure-stack-edge-deploy-prep.md)。
