---
title: Azure 虚拟机上的 TmaxSoft OpenFrame 入门
description: 在 Azure 虚拟机 (VM) 上使用 TmaxSoft OpenFrame 环境重新托管 IBM z/OS 大型机工作负载。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 346c331596f984cbb068c03110997c2478499488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950665"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Azure 上的 TmaxSoft OpenFrame 入门

获取现有的大型机资产，并使用 TmaxSoft OpenFrame 将它们移动到 Microsoft Azure。 此常见的重新托管解决方案会在 Azure 上创建一个模拟环境，使你能够快速迁移应用程序。 无需重格式化。

## <a name="openframe-rehosting-environment"></a>OpenFrame 重新托管环境

在 Azure 上设置适用于开发、演示、测试或生产工作负载的 OpenFrame 环境。 如下图所示，OpenFrame 包括用于在 Azure 上创建大型机模拟环境的多个组件。 例如，OpenFrame 联机服务取代了大型机中间件，如 IBM 客户信息控制系统 (CICS)。 OpenFrame Batch 及其 TJES 组件取代了 IBM 大型机的作业输入子系统 (JES)。 

![OpenFrame 重新托管进程](media/openframe-01.png)

> [!NOTE]
> 若要在 Azure 上运行 OpenFrame 环境，必须具有 TmaxSoft 提供的有效的产品许可证或试用版许可证。

## <a name="openframe-components"></a>OpenFrame 组件

以下组件是 Azure 上的 OpenFrame 环境的一部分：

- 迁移工具包括 OFMiner 解决方案，用于分析大型机资产，然后将它们迁移到 Azure。
- 编译器，其中包括 OFCOBOL（用于解释大型机的 COBOL 程序）、OFPLI（用于解释大型机的 PL/I 程序）以及 OFASM（用于解释大型机的组装器程序）。
- 前端组件，包括 Java 企业用户解决方案 (JEUS )（一种经过 Java Enterprise Edition 6.OFGW 认证的 Web 应用程序服务器）和 OpenFrame 网关组件（提供 3270 侦听器）。
- 应用程序环境。 OpenFrame Base 中间件，用于管理整个系统。 OpenFrame Server Type C (OSC) 取代了大型机的中间件和 IBM CICS。
- 关系数据库，如 Tibero（已显示）、Oracle Database、Microsoft SQL Server、IBM Db2 或 MySQL。 OpenFrame 应用程序使用开放式数据库连接 (ODBC) 协议与数据库进行通信。
- TACF 安全服务模块，用于控制用户对系统和资源的访问。 
- OFManager 是一种解决方案，用于在 Web 环境中提供 OpenFrame 的操作和管理功能。

![OpenFrame 体系结构](media/openframe-02.png)

## <a name="next-steps"></a>后续步骤

- [在 Azure 上安装 TmaxSoft OpenFrame](./install-openframe-azure.md)
