---
title: 如何扫描 Azure 文件
description: 本操作方法指南介绍了如何扫描 Azure 文件的详细信息。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552190"
---
# <a name="register-and-scan-azure-files"></a>注册并扫描 Azure 文件

## <a name="supported-capabilities"></a>支持的功能

Azure 文件支持完整和增量扫描，以根据系统和客户分类来捕获元数据并应用元数据的分类。

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，请创建一个 Azure 监控范围帐户。 有关创建监控范围帐户的详细信息，请参阅 [快速入门：创建 Azure 监控范围帐户](create-catalog-portal.md)。
- 若要设置和计划扫描，你必须是数据源管理员，有关详细信息，请参阅 [目录权限](catalog-permissions.md) 。

## <a name="register-an-azure-files-storage-account"></a>注册 Azure 文件存储帐户

若要在数据目录中注册新的 Azure 文件帐户，请执行以下操作：

1. 导航到监控范围数据目录。
1. 在左侧导航栏中选择 " **管理中心** "。
1. 选择 "源"**和 "扫描**" 下的 **数据源**。
1. 选择“+ 新建”  。
1. 在 " **注册源**" 中，选择 " **Azure 文件**"。 选择“继续”。 

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="注册新的数据源" border="true":::

在 "将 **源 (Azure 文件)** " 屏幕上，执行以下操作：

1. 输入将在目录中列出的数据源的 **名称** 。
1. 选择要指向所需存储帐户的方式：
   1. 选择 " **从 azure** 订阅"，从 " **azure 订阅** " 下拉框中选择适当的订阅，并从 " **存储帐户名称** " 下拉框中选择相应的存储帐户。
   1. 或者，可以选择 " **手动输入** "，然后输入服务终结点 (URL) 。
1. **完成** 注册数据源。

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="注册源选项" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>设置扫描的身份验证

若要使用帐户密钥设置 Azure 文件存储的身份验证，请执行以下操作：

1. 选择 "身份验证方法即 **帐户密钥**"。
2. 选择 " **从 Azure 订阅** " 选项。
3. 选择 Azure 文件帐户所在的 Azure 订阅。
4. 从列表中选择存储帐户名称。
5. 单击“完成”。

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure 监控范围数据目录](how-to-browse-catalog.md)
- [搜索 Azure 监控范围数据目录](how-to-search-catalog.md)