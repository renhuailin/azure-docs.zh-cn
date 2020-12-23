---
title: 在提升的访问权限期间禁止操作
description: VMware 引擎会恢复这些更改，以确保在 VMware 引擎检测到以下任何禁止的操作时服务保持不中断。
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2020
ms.locfileid: "92915393"
---
# <a name="forbidden-actions-during-elevated-access"></a>在提升的访问权限期间禁止操作

在提升时间间隔期间，禁止一些操作。 当 VMware 引擎检测到以下任何禁止的操作时，VMware 引擎会恢复这些更改，以确保服务不会中断。

## <a name="cluster-actions"></a>群集操作

- 正在从 vCenter 中删除群集。
- 更改群集上的 vSphere 高可用性 (HA) 。
- 从 vCenter 将主机添加到群集。
- 从 vCenter 中删除群集的主机。

## <a name="host-actions"></a>主机操作

- 删除 ESXi 主机上的数据存储。
- 正在从主机卸载 vCenter 代理。
- 修改主机配置。
- 更改主机配置文件。
- 将主机置于维护模式。

## <a name="network-actions"></a>网络操作

- 删除私有云中 (DVS) 的默认分布式虚拟交换机。
- 从默认的 DVS 中删除主机。
- 导入任何 DVS 设置。
- 重新配置任何 DVS 设置。
- 升级任何 DVS。
- 正在删除管理端口组。
- 编辑管理端口组。

## <a name="roles-and-permissions-actions"></a>角色和权限操作

- 创建全局角色。
- 修改或删除对任何管理对象的权限。
- 修改或删除任何默认角色。
- 提高角色的权限，使其超过云所有者角色。

## <a name="other-actions"></a>其他操作

- 删除任何默认许可证：
  - vCenter Server
  - ESXi 节点
  - NSX-T
  - HCX
- 修改或删除管理资源池。
- 克隆管理 Vm。


## <a name="next-steps"></a>后续步骤
[CloudSimple 维护和更新](cloudsimple-maintenance-updates.md) 
