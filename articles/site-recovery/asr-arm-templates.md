---
title: Azure 资源管理器模板
description: 使用 Azure Site Recovery 功能的 Azure 资源管理器模板。
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720522"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>适用于 Azure Site Recovery 的 Azure 资源管理器模板

下表包含用于使用 Azure Site Recovery 功能的 Azure 资源管理器模板的链接。

| 模板 | 说明 |
|---|---|
|**Azure 到 Azure** | |
| [创建恢复服务保管库](./quickstart-create-vault-template.md)| 创建恢复服务保管库。 此保管库可以用于 Azure 备份和 Azure Site Recovery。 |
| [为 Azure Vm 启用复制](https://aka.ms/asr-arm-enable-replication) | 使用现有的保管库和自定义目标设置为 Azure Vm 启用复制。|
| [触发故障转移和重新保护](https://aka.ms/asr-arm-failover-reprotect) | 触发一组 Azure Vm 的故障转移和重新保护操作。 |
| [运行 Azure Vm 的端到端 DR 流](https://aka.ms/asr-arm-e2e-flow) | 启动完整的端到端灾难恢复流 (为 Azure Vm 启用复制 + 故障转移和重新保护 + 故障回复和重新保护) （也称为540° Flow）。|
|   |   |