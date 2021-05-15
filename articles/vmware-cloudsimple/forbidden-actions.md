---
title: 在访问权限提升期间禁止的操作
description: VMware 引擎会恢复这些更改，以确保在 VMware 引擎检测到以下任何禁止的操作时服务保持不中断。
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92915393"
---
# <a name="forbidden-actions-during-elevated-access"></a>在访问权限提升期间禁止的操作

在提升时间间隔内，禁止执行一些操作。 当 VMware 引擎检测到以下任何禁止的操作时，VMware 引擎会恢复这些更改，以确保服务不会中断。

## <a name="cluster-actions"></a>群集操作

- 从 vCenter 中删除群集。
- 更改群集上的 vSphere 高可用性 (HA)。
- 从 vCenter 向群集中添加主机。
- 从 vCenter 中删除群集的主机。

## <a name="host-actions"></a>主机操作

- 删除 ESXi 主机上的数据存储。
- 从主机卸载 vCenter 代理。
- 修改主机配置。
- 对主机配置文件进行任何更改。
- 将主机置于维护模式。

## <a name="network-actions"></a>网络操作

- 删除私有云中的默认分布式虚拟交换机 (DVS)。
- 从默认 DVS 中删除主机。
- 导入任何 DVS 设置。
- 重新配置任何 DVS 设置。
- 升级任何 DVS。
- 删除管理端口组。
- 编辑管理端口组。

## <a name="roles-and-permissions-actions"></a>角色和权限操作

- 创建全局角色。
- 修改或删除对任何管理对象的权限。
- 修改或删除任何默认角色。
- 将角色的特权提升到高于云所有者角色的权限级别。

## <a name="other-actions"></a>其他操作

- 删除任何默认许可证：
  - vCenter Server
  - ESXi 节点
  - NSX-T
  - HCX
- 修改或删除管理资源池。
- 克隆管理 VM。


## <a name="next-steps"></a>后续步骤
[CloudSimple 维护和更新](cloudsimple-maintenance-updates.md) 
