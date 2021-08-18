---
title: 定义全局用户访问控制
description: 在大型组织中，用户权限可能会很复杂，并且除标准站点和区域结构之外，还可能由全局组织结构确定。
ms.date: 12/08/2020
ms.topic: article
ms.openlocfilehash: 8f5f8df56a5dcb4152fc0ae9fcae3d504d6cf3e2
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015816"
---
# <a name="define-global-access-control"></a>定义全局访问控制

在大型组织中，用户权限可能会很复杂，并且除标准站点和区域结构之外，还可能由全局组织结构确定。

要支持全局以及更复杂的用户访问权限需求，可以基于业务部门、区域和站点创建一个全局业务拓扑。 然后，可以定义有关这些实体的用户访问权限。

使用适用于业务拓扑的访问工具，有助于组织更好地控制用户在 Azure Defender for IoT 平台中管理和分析设备的位置，从而实现零信任策略。

## <a name="about-access-groups"></a>关于访问组

全局访问控制通过创建用户访问组建立。 访问组包含有关哪些用户可以访问特定业务实体的规则。 通过组，可以控制相关业务部门、区域和站点上的特定用户角色对 Defender for IoT 的查看和配置访问权限。

例如，允许来自 Active Directory 组的安全分析师访问所有西欧汽车和玻璃生产线，以及同一区域中的塑料生产线。

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Active Directory 组安全分析师的示意图。":::

在创建访问组之前，我们建议你：

- 仔细设置业务拓扑。 有关业务拓扑的详细信息，请参阅[使用站点地图视图](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)。

- 规划创建的访问组涉及哪些相关用户。 通过两种方式将将用户分配到访问组：

  - **分配 Active Directory 组的组**：确认已设置 Active Directory 实例以便与本地管理控制台集成。
  
  - **分配本地用户**：确认已创建用户。 有关详细信息，请参阅[定义用户](how-to-create-and-manage-users.md#define-users)。

不能将管理员用户分配到访问组。 这些用户默认有权访问所有业务拓扑实体。

## <a name="create-access-groups"></a>创建访问组

本部分介绍如何创建访问组。 默认为创建的第一个组创建全局业务部门和区域。 在定义第一个组时可以编辑默认实体。

创建组：

1. 从本地管理控制台的侧边菜单中，选择“访问组”。

2. 选择 :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::。 在“添加访问组”对话框中，输入访问组的名称。 控制台支持 64 个字符。 指定名称时，使其有助于轻松将此组与其他组区分开来。

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="从中创建访问组的“添加访问组”对话框。":::

3. 如果显示“分配 Active Directory 组”选项，则可以向此访问组分配一组 Active Directory 用户。

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="在“创建访问组”对话框中分配 Active Directory 组。":::

   如果不显示该选项，但你想要在访问组中包含 Active Directory 组，请选择“系统设置”。 在“集成”窗格中，定义组。 输入与 Active Directory 配置中显示完全相同的名称，小写形式。

5. 在“用户”窗格中，向组中分配所需数量的用户。 还可以将用户分配到不同的组。 如果使用这种方式，则必须创建并保存访问组和规则，然后在“用户”窗格中向组分配用户。

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="管理用户的角色，并根据需要对其进行分配。":::

6. 在“为名称添加规则”对话框中，根据业务拓扑的访问要求创建规则。 此处显示的选项取决于你在“企业视图”和“站点管理”窗口中设计的拓扑。 

   可以为每个组创建多个规则。 在多个站点上使用复杂的访问粒度时，可能需要为每个组创建多个规则。 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="为系统添加规则。":::

创建的规则将显示在“添加访问组”对话框中。 在该对话框中可以删除或编辑它们。

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="在此窗口中查看和编辑所有访问组。":::

### <a name="about-rules"></a>关于规则

创建规则时，请注意以下信息：

- 当一个访问组包含多个规则时，规则逻辑将聚合所有规则。 例如，规则 AND 逻辑，而不是 OR 逻辑。

- 要成功应用规则，必须在“站点管理”窗口中为区域分配传感器。

- 每个规则只能分配一个元素。 例如，可以为每个规则分配一个业务部门、一个区域和一个站点。 例如，如果希望一个 Active Directory 组中的用户都有权访问不同区域中的不同业务部门，可为该组创建多个规则。

- 如果更改了某个实体，并且更改会影响规则逻辑，则会删除该规则。 如果对拓扑实体所做的更改会影响规则逻辑，以致所有规则均被删除，则访问组仍将保留，但用户无法登录到本地管理控制台。 系统会通知用户联系其管理员进行登录。

- 如果未选择业务部门或区域，则用户将有权访问所有已定义的业务部门和区域。

## <a name="see-also"></a>另请参阅

[关于 Defender for IoT 控制台用户](how-to-create-and-manage-users.md)
