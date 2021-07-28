---
title: Azure AD 域服务的副本集概念 | Microsoft Docs
description: 了解 Azure Active Directory 域服务中包含哪些副本集，以及它们如何为需要标识服务的应用程序提供冗余。
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: justinha
ms.openlocfilehash: 8bcd3ebef027ec72728be21b0fe1504236f553ba
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058164"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services"></a>Azure Active Directory 域服务的副本集概念和功能

创建 Azure Active Directory 域服务 (Azure AD DS) 托管域时，需定义唯一的命名空间。 此命名空间是域名（如 aaddscontoso.com），两个域控制器 (DC) 随后会部署到所选 Azure 区域。 DC 的这种部署称为副本集。

可以扩展托管域，使每个 Azure AD 租户具有多个副本集。 可以将副本集添加到任何支持 Azure AD DS 的 Azure 区域中的任何对等互连虚拟网络。 如果某个 Azure 区域处于离线状态，则不同 Azure 区域中的其他副本集可为旧版应用程序提供地理灾难恢复。

> [!NOTE]
> 副本集不允许在单个 Azure 租户中部署多个唯一托管域。 每个副本集包含相同的数据。

## <a name="how-replica-sets-work"></a>副本集的工作方式

创建托管域（如 aaddscontoso.com）时，将创建初始副本集。 其他副本集共享相同的命名空间和配置。 对 Azure AD DS 进行的更改（包括配置、用户标识和凭据、组、组策略对象、计算机对象以及其他更改）会应用于使用 AD DS 复制的托管域中的所有副本集。

在虚拟网络中创建每个副本集。 每个虚拟网络都必须与托管域的副本集的每个其他虚拟网络对等互连。 此配置将创建支持目录复制的网格网络拓扑。 一个虚拟网络可以支持多个副本集，前提是每个副本集位于不同的虚拟子网中。

所有副本集都放置在同一 Active Directory 站点中。 因此，所有更改都将使用站点内复制进行传播以实现快速聚合。

> [!NOTE]
> 无法在副本集之间定义单独的站点以及定义复制设置。

下图显示了具有两个副本集的托管域。 第一个副本集使用域命名空间进行创建。 第二个副本集在此之后创建：

![具有两个副本集的示例托管域的示意图](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> 副本集可确保配置副本集的区域中的身份验证服务的可用性。 若要使应用程序在发生区域性服务中断时具有地理冗余，依赖于托管域的应用程序平台也必须位于其他区域。
>
> 副本集不会提供使应用程序正常运行所需的其他服务（如 Azure VM 或 Azure 应用服务）的复原能力。 其他应用程序组件的可用性设计需要考虑构成应用程序的服务的复原能力。

下面的示例显示了具有三个副本集的托管域，以便进一步提供复原能力并确保身份验证服务的可用性。 在这两个示例中，应用程序工作负载都与托管域副本集位于同一区域中：

![具有三个副本集的示例托管域的示意图](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>部署注意事项

托管域的默认 SKU 为 Enterprise SKU，它支持多个副本集。 如果更改为标准 SKU，则需创建其他副本集，请[将托管域升级](change-sku.md)到 Enterprise 或 Premium  。

支持的副本集最大数量为 5，包括创建托管域时创建的第一个副本。

每个副本集的计费基于域配置 SKU。 例如，如果你有一个使用 Enterprise SKU 的托管域，且有三个副本集，则对于三个副本集中的每个副本集，你的订阅都将按小时计费。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>能否在订阅中创建与托管域不同的副本集？

不是。 副本集必须与托管域位于同一订阅中。

### <a name="how-many-replica-sets-can-i-create"></a>可以创建多少个副本集？

你最多可以创建 5 个副本集 - 托管域的初始副本集外加 4 个附加副本集。

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>如何将用户和组信息同步到我的副本集？

所有副本集都使用网格虚拟网络对等互联彼此连接。 一个副本集接收来自 Azure AD 的用户和组更新。 然后，使用跨对等互连网络的站点内 AD DS 复制将这些更改复制到其他副本集。

与本地 AD DS 一样，扩展的离线状态可能导致复制中断。 由于对等互连虚拟网络不可传递，因此副本集的设计要求需要完全网格化的网络拓扑。

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>拥有副本集后，如何在托管域中进行更改？

托管域中的更改会像其以前一样工作。 你可以[使用已加入托管域的 RSAT 工具创建和使用管理 VM](tutorial-create-management-vm.md)。 可以根据需要将任意数量的管理 VM 加入到托管域。

## <a name="next-steps"></a>后续步骤

若要开始使用副本集，请[创建并配置 Azure AD DS 托管域][tutorial-create-advanced]。 部署后，[创建并使用其他副本集][create-replica-set]。

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
