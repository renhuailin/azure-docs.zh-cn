---
title: 如何扫描 Azure 文件
description: 本操作指南详细介绍了如何扫描 Azure 文件。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/22/2021
ms.openlocfilehash: 39e720f35a591ac7075b5723f3e577151e698371
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114605154"
---
# <a name="register-and-scan-azure-files"></a>注册 Azure 文件并进行扫描

## <a name="supported-capabilities"></a>支持的功能

Azure 文件支持完整扫描和增量扫描，以根据系统默认规则和客户分类规则来捕获元数据和分类。

对于 csv、tsv、psv、ssv 等文件类型，当满足以下逻辑时，系统将提取架构：

1. 第一行值非空
2. 第一行值是唯一的
3. 第一行值既不是日期也不是数字

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，先创建一个 Azure Purview 帐户。 有关创建 Purview 帐户的详细信息，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。
- 要设置和计划扫描，你必须是数据源管理员；有关详细信息，请参阅[目录权限](catalog-permissions.md)。

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

目前，只有一种方法可以设置 Azure 文件存储的身份验证：

- 帐户密钥

### <a name="account-key"></a>帐户密钥

如果选择的身份验证方法是“帐户密钥”，则需要获取访问密钥，并将其存储在密钥保管库中：

1. 转到你的存储帐户
1. 依次选择“设置”>“访问密钥”
1. 复制密钥，并将其保存在某个位置，以备后续步骤使用
1. 导航到你的密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”，然后输入“名称”和“值”作为存储帐户中的密钥
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用密钥[新建凭据](manage-credentials.md#create-a-new-credential)来设置扫描

## <a name="register-an-azure-files-storage-account"></a>注册一个 Azure 文件的存储帐户

要在数据目录中注册新的 Azure 文件帐户，请执行以下操作：

1. 导航到 Purview Data Studio。
1. 在左侧导航区域中选择“数据映射”。
1. 选择“注册”
1. 在“注册源”上，选择“Azure 文件” 
1. 选择“继续”

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="注册新数据源" border="true":::

在“注册源（Azure 文件）”屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。
2. 选择 Azure 订阅，以筛选 Azure 存储帐户。
3. 附加 Azure 存储帐户。
4. 选择集合或创建新集合（可选）。
5. 选择“注册”以注册数据源。

:::image type="content" source="media/register-scan-azure-files/azure-file-register-source.png" alt-text="注册源选项" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下步骤：

1. 在 Purview Studio 的左窗格中选择“数据映射”选项卡。

1. 选择已注册的 Azure 文件存储源。

1. 选择“新建扫描”

1. 选择用于连接到数据源的帐户密钥凭据。 

   :::image type="content" source="media/register-scan-azure-files/set-up-scan-azure-file.png" alt-text="设置扫描":::

1. 可以通过选择列表中相应的项，将扫描范围限定到特定的数据库。

   :::image type="content" source="media/register-scan-azure-files/azure-file-scope-your-scan.png" alt-text="限定扫描范围":::

1. 然后选择扫描规则集。 可以在系统默认项和现有的自定义规则集之间选择，或者可以以内联方式创建新规则集。

   :::image type="content" source="media/register-scan-azure-files/azure-file-scan-rule-set.png" alt-text="扫描规则集":::

1. 选择扫描触发器。 可以设置一项日程安排或运行一次扫描。

   :::image type="content" source="media/register-scan-azure-files/trigger-scan.png" alt-text="trigger":::

1. 查看扫描并选择“保存并运行”。


[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
