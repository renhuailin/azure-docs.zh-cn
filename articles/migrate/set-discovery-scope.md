---
title: 使用 Azure Migrate 设置 VMware 上服务器的发现范围
description: 介绍如何使用 Azure Migrate 为托管在 VMware 上的服务器评估和迁移设置发现范围。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775351"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>为 VMware 环境中的服务器设置发现范围

本文介绍如何在以下情况下限制 VMware 环境中服务器的发现范围：

- 使用 Azure Migrate 发现和评估工具时，使用 [Azure Migrate 设备](migrate-appliance-architecture.md)发现服务器。
- 使用 Azure Migrate 服务器迁移工具时，使用 [Azure Migrate 设备](migrate-appliance-architecture.md)发现服务器，以便以无代理方式将服务器从 VMware 环境迁移到 Azure。

设置设备时，它将连接到 vCenter Server 并启动发现。 在将设备连接到 vCenter Server 之前，可以将发现范围限制为 vCenter Server 数据中心、群集、群集文件夹、主机、主机文件夹或单个服务器。 若要设置范围，请为设备用于访问 vCenter Server 的帐户分配权限。

## <a name="before-you-start"></a>准备工作

如果尚未设置 Azure Migrate 用于发现的 vCenter 用户帐户，请立即执行此操作，以进行[评估](./tutorial-discover-vmware.md#prepare-vmware)或[无代理迁移](./migrate-support-matrix-vmware-migration.md#agentless-migration)。


## <a name="assign-permissions-and-roles"></a>分配权限和角色

可以使用以下两种方法之一为 VMware 清单对象分配权限：

- 在设备使用的帐户上，为你要限定范围的对象分配具有所需权限的角色。
- 或者，将角色分配给数据中心级别的帐户，并传播到子对象。 然后，对于不想要包含在范围内的每个对象，为帐户提供“无访问权限”角色。 不建议使用此方法，因为这种方法很繁琐，可能会公开访问控制，因为每个新的子对象会自动授予从父对象继承的访问权限。

不能在 vCenter server 文件夹级别确定清单发现的范围。 如果需要将发现范围限定于文件夹中的服务器，请创建一个用户，并分别向每个所需的服务器授予访问权限。 支持主机和群集文件夹。


### <a name="assign-a-role-for-assessment"></a>为评估分配角色

1. 在用于发现的设备 vCenter 帐户上，将所有托管服务器的只读角色应用于要发现和评估的所有父对象（主机、群集、主机文件夹、群集文件夹，一直到数据中心）。
2. 将这些权限传播到层次结构中的子对象。

    ![分配权限](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>为无代理迁移分配角色

1. 在用于迁移的设备 vCenter 帐户上，将具有[所需权限](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)的用户定义的角色应用于托管要发现和迁移的服务器的所有父对象。
2. 可以将角色命名为更易于识别的内容。 例如，<em>Azure_Migrate</em>。

## <a name="work-around-for-server-folder-restriction"></a>服务器文件夹限制解决方法

目前，如果在 vCenter 服务器文件夹级别授予访问权限，则 Azure Migrate 发现和评估工具无法发现服务器。 如果你确实想要按服务器文件夹来确定发现和评估的范围，请使用此解决方法。

1. 为要确定发现和评估范围的文件夹中的所有服务器分配只读权限。
2. 向托管服务器主机、群集、主机文件夹、群集文件夹，一直到数据中心的所有父对象授予只读访问权限。 不需将权限传播到所有子对象。
3. 若要使用凭据进行发现，请选择数据中心作为“收集范围”。


基于角色的访问控制设置可确保相应的 vCenter 用户帐户仅有权访问特定于租户的服务器。


## <a name="next-steps"></a>后续步骤

[设置设备](how-to-set-up-appliance-vmware.md)