---
title: 使用 VMware HCX 部署灾难恢复
description: 了解如何通过 VMware HCX 灾难恢复部署虚拟机 (VM) 灾难恢复。 另外了解如何使用 Azure VMware 解决方案作为恢复站点或目标站点。
ms.topic: how-to
ms.date: 06/10/2021
ms.openlocfilehash: 51ca0b13286b32cf208ad9c5ee1e4c25b26a966b
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112021603"
---
# <a name="deploy-disaster-recovery-using-vmware-hcx"></a>使用 VMware HCX 部署灾难恢复

你将在本文使用 VMware HCX 解决方案部署虚拟机 (VM) 灾难恢复以及使用 Azure VMware 解决方案私有云作为恢复站点或目标站点。

>[!IMPORTANT]
>虽然是 HCX 的一部分，但不建议对大型部署使用 VMware HCX 灾难恢复 (DR)。 灾难恢复业务流程是 100% 手动的，Azure VMware 解决方案目前没有用于支持手动 HCX DR 故障转移的 runbook 或功能。 对于企业级灾难恢复，请参阅 VMware Site Recovery Manager (SRM) 或 VMware 业务连续性和灾难恢复 (BCDR) 解决方案。

VMware HCX 提供各种操作，在复制策略中提供精细控制和粒度。 可用的操作包括：

- 反向 - 发生灾难后。 反向可将站点 B 设为源站点，站点 A 设为受保护的 VM 现在所在的站点。

- 暂停 - 暂停与所选 VM 关联的当前复制策略。

- 恢复 - 恢复与所选 VM 关联的当前复制策略。

- 删除 - 删除与所选 VM 关联的当前复制策略。

- 立即同步 - 超出范围从源 VM 同步到受保护的 VM。

本指南介绍以下复制方案：

- 保护 VM 或 VM 组。

- 完成 VM 或 VM 组的测试恢复。

- 恢复 VM 或 VM 组。

- 反向保护 VM 或 VM 组。

## <a name="protect-vms"></a>保护 VM

1. 登录到源站点上的“vSphere 客户端”并访问“HCX 插件”。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="vSphere 中的 HCX 选项" border="true":::

1. 进入“灾难恢复”区域，然后选择“保护 VMS”。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="选择保护 VMS" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. 选择源和远程站点。 在此示例中，远程站点应为 Azure VMware 解决方案私有云。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="保护 VM 窗口" border="true":::

1. 如果需要，请选择“默认复制”选项：

   - 启用压缩：对于低吞吐量方案，建议使用。

   - 启用静默：暂停 VM，以确保将一致的副本同步到远程站点。

   - 目标存储：位于 Azure VMware 解决方案私有云中的受保护的 VM 的远程数据存储，应为 vSAN 数据存储。

   - 计算容器：远程 vSphere 群集或资源池。

   - 目标文件夹：远程目标文件夹，是可选的，如果未选择任何文件夹，则 VM 将直接放在选定群集下。

   - RPO：源 VM 与受保护的 VM 之间的同步间隔。 频率从 5 分钟到 24 小时不等。

   - 快照间隔：快照之间的间隔。

   - 快照数：已配置的快照间隔内的总快照数。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="保护 VM 选项" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. 从列表中选择一个或多个 VM，并根据需要配置复制选项。

   默认情况下，VM 继承在默认复制选项中配置的全局设置策略。 对于所选 VM 中的每个网络接口，配置远程“网络端口组”，并选择“完成”以启动保护过程。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="网络接口对象" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. 监视在同一灾难恢复区域中的每个选定 VM 的进程。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="监视保护进度" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. VM 受保护后，可以在“快照”选项卡中查看不同的快照。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="快照列表" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   黄色三角形表示快照和虚拟机尚未在测试恢复操作中进行测试。

   关机的 VM 与开机的 VM 之间存在主要差异。 此图显示了开机的 VM 的同步过程。 它将启动同步过程，直到完成第一个快照（VM 的完整副本），再在配置的间隔内完成下一个快照。 它同步已关机的 VM 的副本，之后 VM 显示为非活动状态，保护操作显示为已完成。  VM 开机后，会启动同步到远程站点的过程。

## <a name="complete-a-test-recover-of-vms"></a>完成 VM 的测试恢复

1. 登录到远程站点上的“vSphere 客户端”（Azure VMware 解决方案私有云）。 
1. 在“HCX 插件”的“灾难恢复”区域中，选择任何 VM 上的垂直省略号以显示操作菜单，然后选择“测试恢复VM”。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="选择“测试恢复 VM”" border="true":::

1. 选择测试选项和要用于测试 VM 的不同状态的快照。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="选择快照并选择测试" border="true":::

1. 选择“测试”后，恢复操作开始。

1. 完成后，即可在 Azure VMware 解决方案私有云 vCenter 中查看新 VM。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="检查恢复操作" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. 在 VM 或其中运行的任何应用程序上完成测试后，请执行清理以删除测试实例。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="清理测试实例" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>恢复 VM

1. 登录到远程站点上的“vSphere 客户端”（Azure VMware 解决方案私有云）并访问“HCX 插件”。

   对于恢复方案，本示例使用了一组 VM。

1. 从列表中选择要恢复的 VM，打开“操作”菜单，然后选择“恢复 VM”。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="恢复 VM" border="true":::

1. 为每个实例配置恢复选项，然后选择“恢复”以启动恢复操作。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="恢复 VM 确认" border="true":::

1. 完成恢复操作后，新 VM 会出现在远程 vCenter Server 清单中。

## <a name="complete-a-reverse-replication-on-vms"></a>在 VM 上完成反向复制

1. 在 Azure VMware 解决方案私有云上登录到“VSphere 客户端”，并访问“HCX 插件”。
   
   >[!NOTE]
   > 确保在开始反向复制之前，已关闭源站点上的原始 VM。 如果 VM 未关闭电源，则操作将失败。

1. 从列表中选择要复制回源站点的 VM，打开“操作”菜单，然后选择“反向”。 
1. 选择“反向”以开始复制。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="在“保护操作”下选择“反向操作”" border="true":::

1. 监视每个 VM 的详细信息部分。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="查看反向操作的结果" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>灾难恢复计划自动化

VMware HCX 当前没有用于创建和自动化灾难恢复计划的内置机制。 但是，VMware HCX 提供一组 REST API，其中包括用于灾难恢复操作的 API。 可在 VMware HCX 管理器中从 URL 访问 API 规范。

这些 API 涵盖了灾难恢复中的以下操作。

- 保护

- 恢复

- 测试恢复

- 计划的恢复

- Reverse

- 查询

- 测试清理

- 暂停

- 继续

- 删除保护

- 重新配置

JSON 中的恢复操作有效负载的示例如下。

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

使用这些 API，可以构建自定义机制来自动创建和执行灾难恢复计划。
