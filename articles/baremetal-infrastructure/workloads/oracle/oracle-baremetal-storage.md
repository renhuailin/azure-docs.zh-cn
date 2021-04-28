---
title: BareMetal for Oracle 工作负载上的存储
description: 了解 BareMetal Infrastructure for Oracle 工作负载提供的存储。
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 685f4d401d05d897b91ad12dbd79f7d175db05c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558561"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>BareMetal for Oracle 工作负载上的存储

本文概述了 BareMetal Infrastructure for Oracle 工作负载提供的存储。

BareMetal Infrastructure for Oracle 提供了 NetApp 网络文件系统 (NFS) 存储。 NFS 存储不需要 Oracle Real Application Clusters (RAC) 认证。 有关详细信息，请参阅[适用于 Linux 群集的 Oracle RAC 技术矩阵](https://www.oracle.com/database/technologies/tech-generic-linux-new.html)。

此存储产品/服务包括来自 OEM 合作伙伴的第 3 层支持，它使用 A700s 或 A800s 存储控制器。

BareMetal Infrastructure 存储提供了以下高级存储功能：

- 通过 dNFS 协议提供的数据/日志/仲裁/FSA 的存储卷。
- 磁盘冗余（防止多达两个磁盘故障）。
- 将数据横向扩展到多个卷，每卷限制为 100 TB。
- 横向扩展到多个存储控制器，最多 12 个控制器。
- 无磁盘级别管理（添加/删除磁盘），由 Infra 自动负责。
- 无需停机即可将文件内容重新分发到不同的卷。
- 能够增加/收缩卷。
- 使用克隆和 SnapVault 进行备份的 SnapCenter 集成。
- 静态数据加密，支持 FIPS (140-2)。

## <a name="next-steps"></a>后续步骤

了解 BareMetal Infrastructure 修补注意事项。

> [!div class="nextstepaction"]
> [BareMetal for Oracle 的修补注意事项](oracle-baremetal-patching.md)

