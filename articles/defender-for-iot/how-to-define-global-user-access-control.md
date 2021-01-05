---
title: 定义全局用户访问控制
description: 在大型组织中，用户权限可能会很复杂，并且除了标准站点和区域结构外，还可能由全局组织结构确定。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/08/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: de3ff6ead1f0dd86e07c86b992a720a676a2095c
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838439"
---
# <a name="define-global-access-control"></a>定义全局访问控制

在大型组织中，用户权限可能会很复杂，并且除了标准站点和区域结构外，还可能由全局组织结构确定。

若要支持全局且更复杂的用户访问权限需求，可以创建基于业务部门、区域和站点的全局业务拓扑。 然后，可以定义有关这些实体的用户访问权限。

使用适用于业务拓扑的访问工具，通过更好地控制用户在 Azure Defender for IoT 平台中管理和分析资产的位置，帮助组织实现零信任策略。

## <a name="about-access-groups"></a>关于访问组

全局访问控制是通过创建用户访问组建立的。 访问组包含有关哪些用户可以访问特定业务实体的规则。 利用组，你可以控制针对相关业务部门、区域和站点上的特定用户角色的 Defender 的查看和配置访问权限。

例如，允许来自 Active Directory 组的安全分析人员访问所有西欧汽车和玻璃生产线，同时提供一个区域中的塑料线。

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="安全分析师 Active Directory 组的示意图。":::

在创建访问组之前，我们建议你：

- 仔细设置业务拓扑。 有关业务拓扑的详细信息，请参阅 [使用站点地图视图](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)。

- 规划与所创建的访问组相关联的用户。 有两个选项可用于将用户分配到访问组：

  - **分配 Active Directory 组组**：验证是否设置了与本地管理控制台集成的 Active Directory 实例。
  
  - **分配本地用户**：验证是否已创建用户。 有关详细信息，请参阅 [定义用户](how-to-create-and-manage-users.md#define-users)。

不能将管理员用户分配到访问组。 默认情况下，这些用户有权访问所有业务拓扑实体。

## <a name="create-access-groups"></a>创建访问组

本部分介绍如何创建访问组。 为创建的第一个组创建默认的全局业务单元和区域。 定义第一个组时，可以编辑默认实体。

若要创建组：

1. 在本地管理控制台的侧边菜单中，选择 " **访问组** "。

2. 选择 :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::。 在 " **添加访问组** " 对话框中，输入访问组的名称。 控制台支持64个字符。 指定名称时，可帮助你轻松地将此组与其他组区分开来。

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="在其中创建访问组的 &quot;添加访问组&quot; 对话框。":::

3. 如果显示了 " **分配 Active Directory 组** " 选项，则可以向此访问组分配一组 Active Directory 的用户。

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="分配 &quot;创建访问组&quot; 对话框中的 Active Directory 组。":::

   如果该选项未出现，并且您想要在访问组中包含 Active Directory 组，请选择 " **系统设置**"。 在 " **集成** " 窗格中，定义组。 输入与在 Active Directory 配置中显示的名称完全相同的组名。

5. 在 " **用户** " 窗格中，将所需数量的用户分配给组。 你还可以将用户分配到不同的组。 如果以这种方式工作，则必须创建并保存访问组和规则，然后从 " **用户** " 窗格将用户分配到组。

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="管理用户的角色，并根据需要对其进行分配。":::

6. 根据业务拓扑的访问要求，在 "为 ***名称* 添加规则**" 对话框中创建规则。 此处显示的选项取决于你在 " **企业视图** " 和 " **站点管理** " 窗口中设计的拓扑。 

   可以为每个组创建多个规则。 当你在多个站点上使用复杂的访问粒度时，可能需要为每个组创建多个规则。 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="将规则添加到系统。":::

你创建的规则将显示在 " **添加访问组** " 对话框中。 在那里，可以删除或编辑它们。

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="在此窗口中查看和编辑所有访问组。":::

### <a name="about-rules"></a>关于规则

创建规则时，请注意以下信息：

- 当访问组包含多个规则时，规则逻辑将聚合所有规则。 例如，规则使用和逻辑，而不是逻辑。

- 若要成功应用规则，必须在 " **站点管理** " 窗口中将传感器分配给区域。

- 每个规则只能分配一个元素。 例如，可以为每个规则分配一个业务部门、一个区域和一个站点。 如果希望一个 Active Directory 组中的用户有权访问不同区域中的不同业务部门，请为该组创建更多规则。

- 如果更改实体，并且更改影响规则逻辑，则会删除该规则。 如果对拓扑实体所做的更改会影响规则逻辑，以便删除所有规则，则访问组仍将保留，但用户无法登录到本地管理控制台。 系统会通知用户联系其管理员进行登录。

- 如果未选择业务部门或区域，则用户将有权访问所有已定义的业务单元和区域。

## <a name="see-also"></a>另请参阅

[关于用于 IoT 控制台用户的 Defender](how-to-create-and-manage-users.md)
