---
title: 为 Oracle 预配 BareMetal
description: 了解如何为 Oracle 预配 BareMetal 基础结构。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/14/2021
ms.openlocfilehash: 59618d43d720fe4964d1e2971865f83572e681d9
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578544"
---
# <a name="provision-baremetal-for-oracle"></a>为 Oracle 预配 BareMetal

本文介绍如何为 Oracle 工作负载预配 BareMetal 基础结构实例。 

预配 BareMetal 实例的第一步是使用 Microsoft CSA。 它们将根据特定的工作负载需求和所部署的体系结构（无论是单实例、单节点 RAC 还是 RAC）来为你提供帮助。 有关这些拓扑的详细信息，请参阅[适用于 Oracle 的 BareMetal 基础结构的体系结构](oracle-baremetal-architecture.md)。

## <a name="prerequisites"></a>必备条件

> [!div class="checklist"]
> * 有效的 Azure 订阅
> * Microsoft 顶级支持合同
> * Red Hat Enterprise Linux 7.6 的许可证
> * Oracle 支持合同 
> * Oracle 的许可证和软件安装组件
> * ExpressRoute 从本地连接到 Azure（可选择将直接连接的 ExpressRoute Global Reach 从本地配置到 Oracle Database）    
> * 虚拟网络
> * 创建网关
> * 虚拟网络（跳转盒）中的虚拟机 (VM)

## <a name="information-to-provide-microsoft-operations"></a>提供 Microsoft 操作的信息

需要向 CSA 提供以下信息：

1. 虚拟网络地址空间。 此范围必须是 /24 子网，例如 10.11.0.0/24。
2. P2P 范围。 此范围必须是 /29 子网，例如 10.12.0.0/29。
3. 服务器 IP 地址池。 建议的范围为/24；例如 10.13.0.0/24。
4. 服务器 IP 地址。 从服务器 IP 地址池中选择一个 IP 地址。

    > [!Note] 
    > 保留前 30 个 IP 地址用于 Microsoft 基础结构配置。 因此，在此示例中，边栏选项卡的第一个可用 IP 地址将为 10.13.0.30。

5. 需要 Azure 区域；例如“美国西部 2”。
6. 需要 BareMetal 基础结构 SKU；例如，S192（两个计算机）。

## <a name="storage-requirements"></a>存储要求

在预配请求期间，为存储需求（包括基于未来增长的预期存储需求）与 CSA 代表合作。 增加了存储空间，增量为 1 TB。

对于卷，我们遵循 Oracle 的[最佳灵活体系结构 (OFA) 标准](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093)，以及标准层和企业配置。 如果有自定义的存储要求，而不是标准的“T 恤尺寸分类法”，请通过 CSA 发出自定义请求。

| 基本配置（POC/测试） | 说明 | 小型 | 中型 | 大型 |
| --- | --- | --- | --- | --- |
| /u01 | Oracle 二进制文件 | 500 GB | 500 GB | 500 GB |
| /u02 | 读取密集型/管理 | 500 GB | 1 TB | 5 TB |
| /u03 | 写入密集型/日志 | 500 GB | 1 TB | 5 TB |
| /u09 | 备份 | 5 TB | 10 TB | 15 TB |

| 企业配置 | 说明 | 小型 | 中型 | 大型 | 特大型 |
| --- | --- | --- | --- | --- | --- |
| /u01 | Oracle 二进制文件 | 500 GB | 500 GB | 500 GB | 500 GB |
| /u02 | 管理员 | 100 GB | 100 GB | 100 GB | 100 GB |
| /u10 到 /u59 | 读取密集型 | 500 GB | 5 TB | 10 TB | 20 TB |
| /u60 到 /u89 | 写入密集型 | 500 GB | 5 TB | 10 TB | 20 TB |
| /u90 到 /u91 | 重做日志 | 500 GB | 500 GB | 1 TB | 1 TB |
| /u95 | 存档 | 10 TB | 10 TB | 20 TB | 20 TB |
| /u98 | 备份 | 25 TB | 25 TB | 50 TB | 50 TB |

## <a name="next-step"></a>后续步骤

详细了解适用于 Oracle 的 BareMetal 基础结构。

> [!div class="nextstepaction"]
> [什么是 Azure 中的 BareMetal 基础结构？](../../concepts-baremetal-infrastructure-overview.md)
