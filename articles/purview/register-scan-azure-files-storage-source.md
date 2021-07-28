---
title: 如何扫描 Azure 文件
description: 本操作指南详细介绍了如何扫描 Azure 文件。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: c88134e978615d53bdfbde26492096212c3f582f
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656393"
---
# <a name="register-and-scan-azure-files"></a>注册 Azure 文件并进行扫描

## <a name="supported-capabilities"></a>支持的功能

Azure 文件支持完整扫描和增量扫描，以根据系统和客户分类来捕获元数据并应用元数据的分类。

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，先创建一个 Azure Purview 帐户。 有关创建 Purview 帐户的详细信息，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。
- 要设置和计划扫描，你必须是数据源管理员，有关详细信息，请参阅[目录权限](catalog-permissions.md)。

## <a name="register-an-azure-files-storage-account"></a>注册一个 Azure 文件的存储帐户

要在数据目录中注册新的 Azure 文件帐户，请执行以下操作：

1. 导航到 Purview 数据目录。
1. 在左侧导航中选择“管理中心”。
1. 在“源和扫描”下，选择“数据源” 。
1. 选择“+ 新建”  。
1. 在“注册源”上选择“Azure 文件” 。 选择“继续”。

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="注册新数据源" border="true":::

在“注册源（Azure 文件）”屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。
2. 选择 Azure 订阅，以筛选 Azure 存储帐户。
3. 附加 Azure 存储帐户。
4. 选择集合或创建新集合（可选）。
5. 选择“注册”以注册数据源。

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="注册源选项" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>为扫描设置身份验证

要使用帐户密钥设置 Azure 文件存储的身份验证，请执行以下操作：

1. 选择“帐户密钥”作为身份验证方法。
2. 选择“从 Azure 订阅”选项。
3. 选择 Azure 文件帐户所在的 Azure 订阅。
4. 从列表中选择存储帐户名城。
5. 单击“完成”  。

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)