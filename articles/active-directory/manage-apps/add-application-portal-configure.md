---
title: 快速入门：配置企业应用程序属性
titleSuffix: Azure AD
description: 在 Azure Active Directory 中配置企业应用程序的属性。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: f6325dfceaa0ee6cedc60e5555b177e1f7a25d6c
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058250"
---
# <a name="quickstart-configure-enterprise-application-properties-in-azure-active-directory"></a>快速入门：在 Azure Active Directory 中配置企业应用程序属性

本快速入门介绍如何使用 Azure Active Directory 管理中心来配置之前添加到 Azure Active Directory (Azure AD) 租户的企业应用程序的属性。

你可以配置企业应用程序的以下通用属性：

- **启用以供用户登录?** - 决定分配到应用程序的用户能否登录。
- **需要进行用户分配?** - 决定未分配到应用程序的用户能否登录。
- **对用户可见?** - 决定分配到应用程序的用户能否在“我的应用”和 Microsoft 365 应用启动器中看到它。 （请参阅 Microsoft 365 网站左上角的华夫饼菜单。）
- **徽标** - 确定表示应用程序的徽标。
- **注释** - 提供一个位置来添加适用于应用程序的注释。

建议使用非生产环境来测试本快速入门中的步骤。

## <a name="prerequisites"></a>先决条件

若要配置企业应用程序的属性，需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 完成[快速入门：添加企业应用程序](add-application-portal.md)中的步骤。

## <a name="configure-application-properties"></a>配置应用程序属性

应用程序属性控制应用程序的表示方式和访问应用程序的方式。

编辑应用程序属性：

1. 转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)，并使用先决条件中列出的角色之一登录。
1. 在左侧菜单中，选择“企业应用程序”。 此时会打开“所有应用程序”窗格，其中显示了 Azure AD 租户中应用程序的列表。 搜索并选择要使用的应用程序。 例如“Azure AD SAML Toolkit 1”。
1. 在“管理”部分中，选择“属性”以打开“属性”窗格进行编辑  。
1. 选择“是”或“否”来决定是否允许用户登录应用程序。
1. 选择“是”或“否”来决定是否只有分配到应用程序的用户帐户才能登录。
1. 选择“是”或“否”来决定分配到应用程序的用户能否在“我的应用”和 Microsoft 365 门户中看到它。 

    :::image type="content" source="media/add-application-portal-configure/configure-properties.png" alt-text="配置企业应用程序的属性。":::

## <a name="use-a-custom-logo"></a>使用自定义徽标

应用程序徽标显示在“我的应用”和 Microsoft 365 门户上，以及当管理员在企业应用程序库中查看此应用程序时显示。 自定义徽标的大小必须精确到 215x215 像素，并且必须采用 PNG 格式。 建议在应用程序徽标中使用不透明的纯色背景，以便向用户呈现最佳效果。

若要使用自定义徽标，请执行以下操作：

1. 创建一个 215 x 215 像素的徽标，将其保存为 .png 格式。
1. 选择“选择文件”中的图标以上传徽标。
1. 完成后，选择“保存”。

徽标缩略图不会立即更新。 可以关闭并重新打开“属性”窗格以查看更新的缩略图。

## <a name="add-notes"></a>添加注释

可使用“注释”属性来添加与 Azure AD 中的应用程序管理相关的任何信息。 “注释”属性是免费文本字段，最大大小为 1024 个字符。

若要为应用程序输入注释，请执行以下操作：

1. 输入要与应用程序一起保留的注释。
1. 选择“保存” 。

## <a name="clean-up-resources"></a>清理资源

如果你打算完成下一个快速入门，请保留你创建的企业应用程序。 否则，可以考虑将其删除以清理租户。 有关详细信息，请参阅[删除应用程序](delete-application-portal.md)。

## <a name="next-steps"></a>后续步骤

了解如何搜索并查看 Azure AD 租户中的应用程序。
> [!div class="nextstepaction"]
> [查看应用程序](view-applications-portal.md)
