---
title: 如何注册和扫描 Azure Blob 存储
description: 了解如何在 Azure Purview 数据目录中扫描 Azure Blob 存储。
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 40105f18cce8fe515350903837f49d273bd39d03
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209977"
---
# <a name="register-and-scan-azure-blob-storage"></a>注册和扫描 Azure Blob 存储

本文概述了如何在 Purview 中注册 Azure Blob 存储帐户并创建扫描。

## <a name="supported-capabilities"></a>支持的功能

Azure Blob 存储支持通过完整扫描和增量扫描来捕获元数据和架构。 它还可以根据系统和自定义的分类规则对数据进行自动分类。

对于 csv、tsv、psv、ssv 等文件类型，当满足以下逻辑时，系统将提取架构：

1. 第一行值非空
2. 第一行值是唯一的
3. 第一行值既不是日期也不是数字

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，先创建一个 Azure Purview 帐户。 有关创建 Purview 帐户的详细信息，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。
- 你需要是 Azure Purview 数据源管理员

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

有三种方法可以为 Azure Blob 存储设置身份验证：

- 托管标识
- 帐户密钥
- Service Principal

### <a name="managed-identity-recommended"></a>托管标识（推荐）

如果选择“托管标识”，必须先向 Purview 帐户授予扫描数据源的权限，才能建立连接：

1. 导航到自己的存储帐户。
1. 在左侧导航栏菜单中，选择“访问控制 (IAM)”。 
1. 选择“+ 添加”。
1. 将“角色”设置为“存储 Blob 数据读取者”，然后在“选择”输入框下输入 Azure Purview 帐户名。 然后，选择“保存”，将此角色分配给 Purview 帐户。

> [!Note]
> 有关更多详情，请参阅[使用 Azure Active Directory 授予对 Blob 和队列的访问权限](../storage/blobs/authorize-access-azure-active-directory.md)中的步骤

### <a name="account-key"></a>帐户密钥

如果选择的身份验证方法是“帐户密钥”，则需要获取访问密钥，并将其存储在密钥保管库中：

1. 转到你的存储帐户
1. 依次选择“设置”>“访问密钥”
1. 复制密钥，并将其保存在某个位置，以备后续步骤使用
1. 导航到你的密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”，然后输入“名称”和“值”作为存储帐户中的密钥
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用密钥[新建凭据](manage-credentials.md#create-a-new-credential)来创建扫描

### <a name="service-principal"></a>服务主体

若要使用服务主体，可以使用现有的服务主体，也可以创建一个新的服务主体。 

> [!Note]
> 如果必须创建新的服务主体，请执行以下步骤：
> 1. 导航到 [Azure 门户](https://portal.azure.com)。
> 1. 从左侧菜单中选择“Azure Active Directory”。
> 1. 选择 **“应用注册”**。
> 1. 选择“+ 新建应用程序注册”。
> 1. 为应用程序输入名称（服务主体名称）。
> 1. 选择“仅此组织目录中的帐户”。
> 1. 对于重定向 URI，选择“Web”并输入所需的任何 URL；它无需是真实的 URL 或有效的 URL。
> 1. 然后选择“注册”。

需要获取服务主体的应用程序 ID 和机密：

1. 导航到 [Azure 门户](https://portal.azure.com)中的服务主体
1. 复制“概述”中的“应用程序(客户端) ID”和“证书和机密”中的“客户端机密”的值   。
1. 导航到你的密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”并输入所选的“名称”和“值”作为服务主体的“客户端机密”   
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用服务主体[创建新凭据](manage-credentials.md#create-a-new-credential)以设置扫描

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>向服务主体授予对 Blob 存储的访问权限

1. 导航到自己的存储帐户。
1. 在左侧导航栏菜单中，选择“访问控制 (IAM)”。 
1. 选择“+ 添加”。
1. 将“角色”设置为“存储 Blob 数据读取者”，然后在“选择”输入框下输入服务主体名称或对象 ID。 然后，选择“保存”，以将此角色分配给服务主体。

## <a name="firewall-settings"></a>防火墙设置

> [!NOTE]
> 如果为存储帐户启用了防火墙，则必须在创建扫描时使用“托管标识”身份验证方法。

1. 在 [Azure 门户](https://portal.azure.com)中，转到你的存储帐户
1. 依次转到“设置”>“网络”
1. 在“允许的访问来源”下，选中“选定网络”
1. 在“防火墙”部分中，选中“允许受信任的 Microsoft 服务访问此存储帐户”，然后单击“保存”

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="展示了防火墙设置的屏幕截图":::

## <a name="register-an-azure-blob-storage-account"></a>注册 Azure Blob 存储帐户

若要在数据目录中注册新的 blob 帐户，请执行以下操作：

1. 从门户中的 Purview 帐户导航到 [Purview Studio](https://web.purview.azure.com/resource/)。
1. 在 Purview Studio 主页上选择“注册源”。
1. 选择“注册”
1. 在“注册源”上，选择“Azure Blob 存储”
1. 选择“继续”

在“注册源(Azure Blob 存储)”屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。 
1. 选择你的订阅以筛选存储帐户。
1. 选择一个存储帐户。
1. 选择一个集合或创建新集合（可选）。
1. 选择“注册”以注册数据源。

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="注册源选项" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 在 [Purview Studio](https://web.purview.azure.com/resource/) 的左窗格中选择“数据映射”选项卡。

1. 选择你已注册的 Azure Blob 数据源。

1. 选择“新建扫描”

1. 选择要连接到数据源的凭据。 

   :::image type="content" source="media/register-scan-azure-blob-storage-source/set-up-scan-blob.png" alt-text="设置扫描":::

1. 可以选择列表中适当的项，以便将扫描范围限定在特定的文件夹或子文件夹。

   :::image type="content" source="media/register-scan-azure-blob-storage-source/blob-scope-your-scan.png" alt-text="限定扫描范围":::

1. 然后选择扫描规则集。 可以在系统默认项和现有的自定义规则集之间选择，或者可以以内联方式创建新规则集。

   :::image type="content" source="media/register-scan-azure-blob-storage-source/blob-scan-rule-set.png" alt-text="扫描规则集":::

1. 选择扫描触发器。 可以设置一个计划或运行一次扫描。

   :::image type="content" source="media/register-scan-azure-blob-storage-source/trigger-scan.png" alt-text="trigger":::

1. 查看扫描并选择“保存并运行”。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)