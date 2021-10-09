---
title: 管理 IoT Central 组织 | Microsoft Docs
description: 本文介绍如何创建和管理组织层次结构，以控制哪些用户可以访问 IoT Central 组织中的哪些设备。 使用组织创建多租户 IoT Central 应用程序。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.openlocfilehash: e15e2b095cd6b6876728ff773a7b4617e325d77d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656752"
---
# <a name="manage-iot-central-organizations"></a>管理 IoT Central 组织

组织允许你定义一个层次结构，用于管理哪些用户可以查看 IoT Central 应用程序中的哪些设备。 用户角色决定了他们对所查看设备的权限，以及他们可以访问的体验。

组织是分层的：

:::image type="content" source="media/howto-create-organization/organizations-hierarchy.png" alt-text="显示组织示例层次结构的关系图。" border="false":::

创建第一个组织层次结构时，会看到应用程序的名称位于根目录。 添加的每个组织将成为子组织或叶组织。 组织层次结构使用继承。 例如，在上图所示的层次结构中：

- Adatum Solar 用户有权访问位于美国、加拿大、欧洲和拉丁美洲的设备。
- Contoso 用户有权访问层次结构中所有组织的所有设备。

同级组织中的用户看不到彼此的其他设备。

授予用户对应用程序的访问权限时，你为其分配访问权限的层次结构越高，他们可以看到和管理的内容就越多。 组织控制用户可以看到的设备。 角色定义用户可以执行哪些操作，例如创建、读取和删除设备。

以下屏幕截图显示了 IoT Central 中的组织层次结构定义：

:::image type="content" source="media/howto-create-organization/organizations-definition.png" alt-text="组织层次结构定义屏幕截图。":::

## <a name="create-a-hierarchy"></a>创建层次结构

若要开始使用组织，需要定义组织层次结构。 层次结构中的每个组织都充当一个逻辑容器，你可以在其中放置设备、保存仪表板和设备组以及邀请用户。 要创建组织，请转到 IoT Central 应用程序中的“管理”部分，选择“组织”选项卡，并选择“+ 新建”或使用现有组织的上下文菜单。 要一次创建一个或多个组织，选择“+ 添加另一个组织”：

:::image type="content" source="media/howto-create-organization/create-organizations-hierarchy.png" alt-text="创建组织层次结构的选项的屏幕截图。":::

> [!TIP]
> 组织的初始设置必须由“应用管理员”角色成员完成。

要将组织重新分配给一个新的上级，选择“编辑”并选择一个新的上级。

若要删除组织，必须删除或移至其他组织中任何关联的项，如仪表板，如设备、用户、设备组和作业。

> [!TIP]
> 还可使用 REST API [创建和管理组织](/rest/api/iotcentral/1.1-previewdataplane/organizations)。

## <a name="assign-devices"></a>分配设备

定义组织层次结构后，将设备分配给组织。 每个设备仅属于单个组织，因此请选择层次结构中的相应组织。

在应用程序中创建新设备时，将其分配给层次结构中的组织：

:::image type="content" source="media/howto-create-organization/assign-device.png" alt-text="显示如何将设备分配给组织的屏幕截图。":::

若要向组织分配或重新分配现有设备，请在设备列表中选择该设备，然后选择“组织”：

:::image type="content" source="media/howto-create-organization/change-device-organization.png" alt-text="显示如何更改设备与之关联的组织的屏幕截图。":::

> [!TIP]
> 可以在设备列表中查看设备所属的组织。 使用设备列表中的筛选器工具显示特定组织中的设备。

将设备重新分配给另一个组织时，该设备的数据将根据数据引入的时间保留在该组织中。 例如：

- 设备在第 1 天到第 7 天是 Contoso/Customer1 组织的一部分，然后在第 8 天转移到 Contoso/Customer4 组织。
- 第 9 天，Contoso/Customer4 用户可以看到第 8 天和 9 天的数据。
- 第 9 天，Contoso/Customer1 用户可以看到第 1 天到第 7 天的数据。

### <a name="device-first-registration"></a>设备首次注册

设备可以自行注册 IoT Central 应用程序，而无需先添加到设备列表。 在这种情况下，IoT Central 会将设备添加到层次结构中的根组织。 然后，可以将设备重新分配到不同的组织。

相反，你可以使用 CSV 导入功能向应用程序批量注册设备，并将其分配给组织。 若要了解详细信息，请参阅[导入设备](howto-manage-devices.md#import-devices)。

### <a name="gateways"></a>网关

将网关和叶设备分配给组织。 无需将网关及其关联的叶设备分配给同一组织。 如果将它们分配给不同的组织，则用户可能会看到网关，但不能看到叶设备，或者看到叶设备，但不能看到网关。

## <a name="roles"></a>角色

在应用程序中创建第一个组织时，IoT Central 会在应用程序中添加三个新角色：“组织管理员”、“组织操作员”和“组织查看者”。 这些角色是必需的，因为组织用户无法访问某些应用程序范围的功能，例如定价计划、品牌和颜色、API 令牌和应用程序范围的注册组信息。

:::image type="content" source="media/howto-create-organization/organization-roles.png" alt-text="显示三个组织角色的屏幕截图。":::

在应用程序中邀请用户加入组织时，可以使用这些角色。

### <a name="custom-organization-roles"></a>自定义组织角色

若要为组织用户创建自定义角色，请创建新角色并选择“组织”角色类型：

:::image type="content" source="media/howto-create-organization/custom-organization-role.png" alt-text="显示如何创建自定义组织角色的屏幕截图。":::

然后选择角色权限：

:::image type="content" source="media/howto-create-organization/organization-role-permissions.png" alt-text="显示如何为组织角色选择自定义权限的屏幕截图。":::

## <a name="invite-users"></a>邀请用户

创建组织层次结构并将设备分配给组织后，邀请用户加入应用程序并将其分配给组织。

若要邀请用户，请导航到“管理”>“用户”。 输入其电子邮件地址、分配到的组织，以及用户所属的一个或多个角色。 选择的组织会筛选可用角色列表，以确保将用户分配给有效角色：

:::image type="content" source="media/howto-create-organization/assign-user-organization.png" alt-text="显示如何将用户分配给组织和角色的屏幕截图。":::

可以将同一个用户分配给多个组织。 用户可以在分配到的每个组织中拥有不同的角色：

| 名称 | 角色 | 组织 |
| ---- | ---- | ------------ |
| user1@contoso.com | 组织管理员 | Contoso Inc/Lamna Health |
| user1@contoso.com | 组织查看者 | Contoso Inc/Adatum Solar |

## <a name="use-organizations"></a>使用组织

创建组织层次结构后，可以在应用程序区域使用组织，例如：

- [组织仪表板](howto-manage-dashboards.md)：向用户显示有关其组织中设备的信息。
- [设备组](tutorial-use-device-groups.md)：用于特定组织中的设备。
- [分析](howto-create-analytics.md)：用于特定组织中的设备。
- [作业](howto-manage-devices-in-bulk.md#create-and-run-a-job)：批量管理特定组织中的设备。

## <a name="default-organization"></a>默认组织

可以将组织设置为在应用程序中使用的默认组织。 每当选择组织时，例如将新用户添加到 IoT Central 应用程序时，默认组织都将成为默认选项。

若要设置默认组织，请选择顶部菜单栏上的“设置”：

:::image type="content" source="media/howto-create-organization/set-default-organization.png" alt-text="显示如何设置默认组织的屏幕截图。":::

> [!TIP]
> 这是仅适用于你的个人首选项。

## <a name="add-organizations-to-an-existing-application"></a>将组织添加到现有应用程序

在添加组织层次结构之前，应用程序可能包含设备、用户和体验（如仪表板、设备组和作业）。

开始添加组织时，所有现有设备、用户和体验仍与应用程序中的根组织关联：

- 可以将设备重新分配到新组织。
- 可以将用户分配到新组织，并将其从根目录取消分配。
- 可以重新创建仪表板、设备组和作业等体验，并将其与层次结构中的组织关联。

## <a name="limits"></a>限制

以下限制适用于组织：

- 层次结构不能超过五个级别的深度。
- 组织总数不能超过 200。 层次结构中的每个节点都计为一个组织。

## <a name="next-steps"></a>后续步骤

了解如何管理 Azure IoT Central 组织，建议接下来了解如何[使用数据导出将 IoT 数据导出到云目标](howto-export-data.md)。
