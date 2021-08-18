---
title: 管理 Microsoft Azure Maps Creator
description: 本文介绍如何管理 Microsoft Azure Maps Creator。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: af01febed5398ecb6750305e6d03352f9bcea727
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751470"
---
# <a name="manage-azure-maps-creator"></a>管理 Azure Maps Creator

可以使用 Azure Maps Creator 创建专用室内地图数据。 使用 Azure Maps API 和室内定位模块，可开发动态交互式室内定位 Web 应用程序。 有关定价信息，请参阅[在 Azure Maps 中选择正确的定价层](choose-pricing-tier.md)。

本文将指导你完成在 Azure Maps 帐户中创建和删除 Creator 资源的步骤。

## <a name="create-creator-resource"></a>创建 Creator 资源

1. 登录到 [Azure 门户](https://portal.azure.com)

2. 导航到 Azure 门户菜单。 选择“所有资源”，然后选择你的 Azure Maps 帐户。

      :::image type="content" border="true" source="./media/how-to-manage-creator/select-all-resources.png" alt-text="选择 Azure Maps 帐户":::

3. 在导航窗格中，选择“Creator 概述”，然后选择“创建” 。

    :::image type="content" border="true" source="./media/how-to-manage-creator/creator-blade-settings.png" alt-text="创建 Azure Maps Creator 页":::

4. 输入 Creator 资源的名称、位置和地图预配存储单元。 目前，仅在美国支持 Creator。 选择“查看 + 创建”。

   :::image type="content" source="./media/how-to-manage-creator/creator-creation-dialog.png" alt-text="输入 Creator 帐户信息页":::

5. 检查你的设置，然后选择“创建”。

    :::image type="content" source="./media/how-to-manage-creator/creator-create-dialog.png" alt-text="确认 Creator 帐户设置页":::

    部署完成后，会显示一个页面，其中包含成功或失败消息。

    :::image type="content" source="./media/how-to-manage-creator/creator-resource-created.png" alt-text="资源部署状态页":::

6. 选择“转到资源”。 Creator 资源视图页将显示 Creator 资源和所选人口统计区域的状态。
      :::image type="content" source="./media/how-to-manage-creator/creator-resource-view.png" alt-text="Creator 状态页":::

   >[!NOTE]
   >若要返回 Azure Maps 帐户，请在导航窗格中选择“Azure Maps 帐户”。

## <a name="delete-creator-resource"></a>删除 Creator 资源

删除 Creator 资源：

1. 在 Azure Maps 帐户中，选择“Creator”下的“概述” 。

2. 选择“删除”。

    >[!WARNING]
    >删除 Azure Maps 帐户的 Creator 资源后，还会删除使用 Creator 服务创建的转换、数据集、图块集和功能状态集。

     :::image type="content" source="./media/how-to-manage-creator/creator-delete.png" alt-text="带“删除”按钮的 Creator 页":::

3. 系统会要求你通过键入 Creator 资源的名称来确认删除。 删除资源后，会显示如下所示的确认页面：

     :::image type="content" source="./media/how-to-manage-creator/creator-confirm-delete.png" alt-text="带删除确认的 Creator 页":::

## <a name="authentication"></a>身份验证

Creator 继承 Azure Maps 访问控制 (IAM) 设置。 必须使用身份验证和授权规则发送数据访问的所有 API 调用。

Creator 使用情况数据合并到 Azure Maps 使用情况图表和活动日志中。  有关详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

>[!Important]
>建议使用：
>
> * 使用 Creator 服务通过 Azure Maps 帐户生成的所有解决方案中的 Azure Active Directory (Azure AD)。 有关 Azure AD 的详细信息，请参阅 [Azure AD 身份验证](azure-maps-authentication.md#azure-ad-authentication)。
>
>* 基于角色的访问控制 (RBAC) 设置。 使用这些设置，地图创建者可以充当 Azure Maps 数据参与者角色，而 Creator 地图数据用户可以充当 Azure Maps 数据读取者角色。 有关详细信息，请参阅[使用基于角色的访问控制进行身份验证](azure-maps-authentication.md#authorization-with-role-based-access-control)。

## <a name="access-to-creator-services"></a>访问 Creator 服务

可在地理 URL 访问 Creator 服务和使用 Creator 中托管的数据的服务（例如呈现服务）。 地理 URL 由在创建过程中选择的位置确定。 例如，如果 Creator 是在美国地理位置的一个区域中创建的，则必须将所有对转换服务的调用都提交到 `us.atlas.microsoft.com/conversions`。 若要查看区域到地理位置的映射，[请参阅 Creator 服务地理范围](creator-geographic-scope.md)。

此外，导入到 Creator 的所有数据都应上传到与 Creator 资源相同的地理位置。 例如，如果在美国预配了 Creator，则所有原始数据都应通过 `us.atlas.microsoft.com/mapData/upload` 上传。

## <a name="next-steps"></a>后续步骤

用于室内定位的 Creator 服务简介：

> [!div class="nextstepaction"]
> [数据上传](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [数据转换](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [数据集](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [图块集](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [特征状态集](creator-indoor-maps.md#feature-statesets)

了解如何使用 Creator 服务在应用程序中呈现室内地图：

> [!div class="nextstepaction"]
> [Azure Maps Creator 教程](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [室内定位的动态样式](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [使用室内定位模块](how-to-use-indoor-module.md)
