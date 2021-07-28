---
title: 选择具有 Azure Migrate Server Migration 的 VMware 迁移选项
description: 概述使用 Azure Migrate Server Migration 将 VMware VM 迁移到 Azure 的选项
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 22a0629d50ee8181ffcbfe7dad32ab76fb3e68fd
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714157"
---
# <a name="select-a-vmware-migration-option"></a>选择 VMware 迁移选项

可以使用 Azure Migrate 服务器迁移工具将 VMware VM 迁移到 Azure。 此工具提供多个选项用于迁移 VMware VM：

- 使用无代理复制进行迁移。 迁移 VM 且无需在其上安装任何组件。
- 使用复制代理进行迁移。 在要复制的 VM 上安装代理。


## <a name="compare-migration-methods"></a>比较迁移方法

使用这些选定的比较来帮助你决定使用哪种方法。 还可查看[无代理](migrate-support-matrix-vmware-migration.md#agentless-migration)和[基于代理](migrate-support-matrix-vmware-migration.md#agent-based-migration)的迁移的完整支持要求。

设置 | **无代理** | **基于代理**
--- | --- | ---
**Azure 权限** | 需要有权创建 Azure Migrate 项目，且有权注册在部署 Azure Migrate设备时创建的 Azure AD 应用。 | 需要 Azure 订阅上的参与者权限。 
**复制** | 最多可以从一个 vCenter Server 同时复制 500 个 VM。 在门户中，一次最多可选择 10 台计算机进行复制。 若要复制更多计算机，请分批添加，每次添加 10 台。| 复制容量通过缩放复制设备而增加。
**设备部署** | [Azure Migrate 设备](migrate-appliance.md)在本地部署。 | [Azure Migrate 复制设备](migrate-replication-appliance.md)在本地部署。
**Site Recovery 兼容性** | 可兼容。 | 如果已使用 Site Recovery 为计算机设置复制，则无法使用 Azure Migrate Server Migration 进行复制。
**目标磁盘** | 托管磁盘 | 托管磁盘
**磁盘限制** | OS 磁盘：2 TB<br/><br/> 数据磁盘：32 TB<br/><br/> 最大磁盘数：60 | OS 磁盘：2 TB<br/><br/> 数据磁盘：32 TB<br/><br/> 最大磁盘数：63
**传递磁盘** | 不支持 | 支持
**UEFI 启动** | 支持。 | 支持。 
**连接** | 公共 Internet <br/> ExpressRoute（具有 Microsoft 对等互连） <br/> <br/> [了解如何](./replicate-using-expressroute.md)使用专用终结点通过 ExpressRoute 专用对等互连或 S2S VPN 连接进行复制。 |公共 Internet <br/> ExpressRoute（具有专用对等互连） <br/> ExpressRoute（具有 Microsoft 对等互连） <br/> 站点到站点 VPN

## <a name="compare-deployment-steps"></a>比较部署步骤

查看限制后，了解部署每个解决方案所涉及的步骤可以帮助你确定选择哪个选项。

**任务** | **详细信息** |**无代理** | **基于代理**
--- | --- | --- | ---
**部署 Azure Migrate 设备** | 在 VMware VM 上运行的轻型设备。<br/><br/> 设备用于发现和评估计算机，并使用无代理迁移来迁移计算机。 | 必需。<br/><br/> 如果已设置要评估的设备，则可以使用同一设备进行无代理迁移。 | 不需要。<br/><br/> 如果已设置要评估的设备，可以将其保留在原位，如果已完成评估，可以将其删除。
**使用服务器评估工具** | 使用 Azure Migrate:Server Assessment 工具评估计算机。 | 评估是可选的。 | 评估是可选的。
**使用服务器迁移工具** | 在 Azure Migrate 项目中添加 Azure Migrate 服务器迁移工具。 | 必需 | 必需
**准备 VMware 进行迁移** | 在 VMware 服务器和 VM 上配置设置。 | 必需 | 必需
**在 VM 上安装出行服务** | 出行服务在要复制的每个 VM 上运行 | 不是必需 | 必需
**部署复制设备** | [复制设备](migrate-replication-appliance.md)用于基于代理的迁移。 它连接 VM 上运行的出行服务和 Server Migration。 | 不是必需 | 必需
**复制 VM**。 启用 VM 复制。 | 配置复制设置并选择要复制的 VM | 必需 | 必需
**运行测试迁移** | 运行测试迁移，确保一切按预期正常进行。 | 必需 | 必需
**运行完整迁移** | 迁移 VM。 | 必需 | 必需



## <a name="next-steps"></a>后续步骤

使用无代理迁移来[迁移 VMware VM](tutorial-migrate-vmware.md)。



