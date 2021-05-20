---
title: 注册并扫描 Power BI 租户（预览版）
description: 了解如何使用 Azure Purview 门户注册并扫描 Power BI 租户。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 2ecc5df9db51bb6c923b9e0f47163e492bd76cfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695732"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>注册并扫描 Power BI 租户（预览版）

本文介绍如何使用 Azure Purview 门户注册并扫描 Power BI 租户。

> [!Note]
> 如果 Purview 实例和 Power BI 租户位于同一 Azure 租户中，则只能使用托管标识 (MSI) 身份验证来设置 Power BI 租户扫描。 

## <a name="create-a-security-group-for-permissions"></a>为权限创建安全组

若要设置身份验证，请创建一个安全组，并向其添加 Purview 托管标识。

1. 在 [Azure 门户](https://portal.azure.com)中，搜索“Azure Active Directory”。
1. 按照[使用 Azure Active Directory 创建基本组并添加成员](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)中的说明在 Azure Active Directory 中创建一个新安全组。

    > [!Tip]
    > 如果你已有想要使用的安全组，则可以跳过此步骤。

1. 选择“安全性”作为“组类型” 。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="安全组类型":::

1. 将 Purview 托管标识添加到此安全组。 选择“成员”，然后选择“+ 添加成员” 。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="将目录的托管实例添加到组。":::

1. 搜索 Purview 托管标识并将其选中。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="通过搜索目录来添加目录":::

    你应会看到一条成功通知，显示已添加该标识。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="添加目录 MSI 成功":::

## <a name="associate-the-security-group-with-the-tenant"></a>将安全组与租户关联

1. 登录到 [Power BI 管理门户](https://app.powerbi.com/admin-portal/tenantSettings)。
1. 选择“租户设置”页。

    > [!Important]
    > 你需要成为 Power BI 管理员才能看到“租户设置”页。

1. 选择“管理员 API 设置” > “允许服务主体使用只读 Power BI 管理员 API (预览)” 。
1. 选择“特定安全组”。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="该图像显示如何允许服务主体获取只读 Power BI 管理员 API 权限":::

    > [!Caution]
    > 如果允许创建的安全组（其成员包括 Purview 托管标识）使用只读 Power BI 管理员 API，则还会允许该安全组访问此租户中的所有 Power BI 项目的元数据（例如仪表板和报表名称、所有者、说明等）。 将元数据拉取到 Azure Purview 后，Purview 的权限（而不是 Power BI 权限）会确定谁可以看到该元数据。

    > [!Note]
    > 可以从开发人员设置中删除安全组，但不会从 Purview 帐户中删除之前提取的元数据。 如果愿意，可以单独将其删除。

## <a name="register-your-power-bi-and-set-up-a-scan"></a>注册 Power BI 并设置扫描

授予用于连接到 Power BI 租户的管理员 API 的 Purview 托管标识权限后，现在，可以通过 Azure Purview Studio 设置扫描。

首先，将一个特殊的功能标志添加到 Purview URL 

1. 选择“管理中心”图标。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="“管理中心”图标。":::

1. 然后，在“数据源”上选择“+ 新建” 。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="“新建数据源”按钮的图像":::

    选择“Power BI”作为数据源。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="显示可供选择的数据源列表的图像":::

3. 为 Power BI 实例指定一个易记名称。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="显示 Power BI 数据源易记名称的图像":::

    该名称的长度必须介于 3-63 个字符之间，并且只能包含字母、数字、下划线和连字符。  不允许包含空格。

    默认情况下，系统将查找存在于同一 Azure 订阅中的 Power BI 租户。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI 数据源已注册":::

    > [!Note]
    > 对于 Power BI，只允许对一个实例进行数据源注册和扫描。


4. 为扫描提供一个名称。 然后选择包含或排除个人工作区的选项。 请注意，唯一受支持的身份验证方法是“托管标识”。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="显示 Power BI 扫描设置的图像":::

    > [!Note]
    > * 切换扫描配置以包括或排除个人工作区将触发对 Power BI 源进行完全扫描
    > * 扫描名称的长度必须介于 3-63 个字符之间，并且只能包含字母、数字、下划线和连字符。 不允许包含空格。

5. 设置扫描触发器。 选项包括“一次”、“每 7 天一次”和“每 30 天一次”  。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="扫描触发器图像":::

6. 在“查看新扫描”上，选择“保存并运行”启动扫描 。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="保存并运行 Power BI 屏幕图像":::

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
