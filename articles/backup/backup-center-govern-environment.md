---
title: 使用备份中心管理备份空间
description: 了解如何管理 Azure 环境，以确保从备份角度来看所有资源都与备份中心兼容。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 8b62c2968dccb8d225e472db84c30f9513dd596d
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605106"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>使用备份中心管理备份空间

备份中心有助于管理 Azure 环境，以确保从备份角度来看所有资源都与备份中心兼容。 下面是备份中心的一些管理功能：

* 查看并分配用于备份的 Azure 策略

* 在所有用于备份的内置 Azure 策略上查看资源的合规性。

* 查看所有未配置为进行备份的数据源。

## <a name="supported-scenarios"></a>支持的方案

* 有关支持和不支持的方案的详细列表，请参阅[支持矩阵](backup-center-support-matrix.md)。

## <a name="azure-policies-for-backup"></a>用于备份的 Azure 策略

若要查看所有可用于备份的 [Azure 策略](../governance/policy/overview.md)，请选择“用于备份的 Azure 策略”菜单项。 执行此操作将显示可用于订阅和资源组分配的所有内置和自定义的[备份专用 Azure Policy 定义](policy-reference.md)。

选择任何定义都可[将策略分配](../governance/policy/tutorials/create-and-manage.md#assign-a-policy)给范围。

![选择 Azure Policy 定义](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>备份合规性

单击“备份合规性”菜单项可帮助你根据已分配给 Azure 环境的各种内置策略来查看资源的[合规性](../governance/policy/how-to/get-compliance-data.md)。 你可以查看符合所有策略的资源的百分比，以及具有一个或多个不合规资源的策略。

![查看备份合规性](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>可保护的数据源

选择“可保护的数据源”菜单项可以查看尚未配置备份的所有数据源。 可以按数据源订阅、资源组、位置、类型和标记筛选列表。 确定了需要备份的数据源后，可以右键单击相应的网格项，然后选择“备份”以为资源配置备份。

![可保护的数据源菜单](./media/backup-center-govern-environment/protectable-datasources.png)

> [!NOTE]
> 如果选择“Azure VM 中的 SQL”作为数据源类型，“可保护的数据源”视图将显示所有未配置备份的 SQL 数据库的库 VM 列表 。
> 如果选择“Azure 存储(Azure 文件存储)”作为数据源类型，“可保护的数据源”视图将显示所有未配置文件共享备份的存储帐户（支持文件共享）的列表 。


## <a name="next-steps"></a>后续步骤

* [监视和操作备份](backup-center-monitor-operate.md)
* [使用备份中心执行操作](backup-center-actions.md)
* [获取有关备份的见解](backup-center-obtain-insights.md)