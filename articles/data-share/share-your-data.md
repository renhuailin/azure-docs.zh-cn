---
title: 教程：在组织外共享 - Azure Data Share
description: 教程 - 使用 Azure Data Share 与客户和合作伙伴共享数据
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: b8d49e3b3c6f6407fe241e00ada5039bd94fd706
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870870"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>教程：使用 Azure Data Share 共享数据  

本教程介绍如何设置新的 Azure Data Share，然后开始与 Azure 组织外部的客户和合作伙伴共享数据。 

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建 Data Share。
> * 向 Data Share 添加数据集。
> * 为 Data Share 启用快照计划。 
> * 将接收人添加到 Data Share。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 收件人的 Azure 登录电子邮件地址（使用其电子邮件别名将无效）。
* 如果源 Azure 数据存储位于与你将用于创建 Data Share 资源的 Azure 订阅不同的 Azure 订阅中，请在 Azure 数据存储所在的订阅中注册 [Microsoft.DataShare 资源提供程序](concepts-roles-permissions.md#resource-provider-registration)。 

### <a name="share-from-a-storage-account"></a>从存储帐户共享

* 一个 Azure 存储帐户：如果没有，可以创建一个 [Azure 存储帐户](../storage/common/storage-account-create.md)
* 向存储帐户进行写入的权限，此权限位于 *Microsoft.Storage/storageAccounts/write* 中。 “参与者”角色有此权限。
* 向存储帐户添加角色分配的权限，此权限存在于 *Microsoft.Authorization/role assignments/write* 中。 “所有者”角色有此权限。 


### <a name="share-from-a-sql-based-source"></a>从基于 SQL 的源共享
下面是从 SQL 源共享数据的先决条件列表。 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>从 Azure SQL 数据库或 Azure Synapse Analytics（以前称为 Azure SQL DW）共享的先决条件

* 包含要共享的表和视图的 Azure SQL 数据库或 Azure Synapse Analytics（以前称为 Azure SQL DW）。
* 向 SQL 服务器上的数据库进行写入的权限，此权限存在于 *Microsoft.Sql/servers/databases/write* 中。 “参与者”角色有此权限。
* SQL 服务器的 Azure Active Directory 管理员
* SQL Server 防火墙访问权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中导航到“SQL Server”。 从左侧导航栏中选择“防火墙和虚拟网络”。
    1. 对于“允许 Azure 服务和资源访问此服务器”，单击“是”。
    1. 单击“+ 添加客户端 IP”。 客户端 IP 地址可能会更改。 下次从 Azure 门户共享 SQL 数据时，可能需要重复此过程。 还可以添加 IP 范围。
    1. 单击“ **保存**”。 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>从 Azure Synapse Analytics（工作区）SQL 池共享的先决条件

* * 包含要共享的表的 Azure Synapse Analytics（工作区）专用 SQL 池。 目前不支持共享视图。 目前不支持从无服务器 SQL 池共享。
* 在 Synapse 工作区中写入 SQL 池的权限，该权限位于 Microsoft.Synapse/workspaces/sqlPools/write。 “参与者”角色有此权限。
* Data Share 资源托管标识访问 Synapse 工作区 SQL 池的权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中导航到 Synapse 工作区。 从左侧导航栏中选择“SQL Active Directory 管理员”，并将你自己设置为“Azure Active Directory 管理员”。
    1. 打开 Synapse Studio，从左侧导航栏中选择“管理”。 在“安全性”下选择“访问控制”。 为自己分配“SQL 管理员”或“工作区管理员”角色 。
    1. 在 Synapse Studio 中，从左侧导航栏中选择“开发”。 在 SQL 池中执行以下脚本，以将 Data Share 资源托管标识添加为 db_datareader。 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       请注意， *<share_acc_name>* 是 Data Share 资源的名称。 如果尚未创建 Data Share 资源，则可以稍后返回到该先决条件。  

* Synapse 工作区防火墙访问权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中导航到 Synapse 工作区。 从左侧导航栏中选择“防火墙”。
    1. 对于“允许 Azure 服务和资源访问此工作区”，单击“启用”。
    1. 单击“+ 添加客户端 IP”。 客户端 IP 地址可能会更改。 下次从 Azure 门户共享 SQL 数据时，可能需要重复此过程。 还可以添加 IP 范围。
    1. 单击“ **保存**”。 


### <a name="share-from-azure-data-explorer"></a>从 Azure 数据资源管理器进行共享
* Azure 数据资源管理器群集，其中包含要共享的数据库。
* 向 Azure 数据资源管理器群集进行写入的权限，此权限存在于 *Microsoft.Kusto/clusters/write* 中。 “参与者”角色有此权限。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-data-share-account"></a>创建 Data Share 帐户

### <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 资源组中创建 Azure Data Share 资源。

1. 选择门户左上角的菜单按钮，然后选择“创建资源”(+)。

1. 搜索“Data Share”。 

1. 选择“Data Share”，然后选择“创建”  。

1. 根据以下信息填写 Azure Data Share 资源的基本详细信息。 

     **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 订阅 | 你的订阅 | 选择要用于 Data Share 帐户的 Azure 订阅。|
    | 资源组 | *testresourcegroup* | 使用现有资源组或创建新资源组。 |
    | 位置 | *美国东部 2* | 选择 Data Share 帐户的区域。
    | 名称 | *datashareaccount* | 指定 Data Share 帐户的名称。 |
    | | |

1. 选择“审阅 + 创建”，然后选择“创建”来预配数据共享帐户 。 预配新的 Data Share 帐户通常需要大约 2 分钟或更少的时间。 

1. 部署完成后，选择“转到资源”。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在 Azure 资源组中创建 Azure Data Share 资源。

首先为 Azure CLI 准备环境：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

使用以下命令创建资源：

1. 使用 [az account set](/cli/azure/account#az_account_set) 命令将订阅设置为当前默认订阅：

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. 运行 [az provider register](/cli/azure/provider#az_provider_register) 命令注册资源提供程序：

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. 运行 [az group create](/cli/azure/group#az_group_create) 命令创建资源组或使用现有资源组：

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. 运行 [az datashare account create](/cli/azure/datashare/account#az_datashare_account_create) 命令创建 Data Share 帐户：

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   运行 [az datashare account list](/cli/azure/datashare/account#az_datashare_account_list) 命令查看 Data Share 帐户：

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>创建共享

### <a name="portal"></a>[门户](#tab/azure-portal)

1. 导航到 Data Share 概览页。

    ![共享数据](./media/share-receive-data.png "共享数据") 

1. 选择“开始共享数据”  。

1. 选择“创建”  。   

1. 填写共享的详细信息。 指定名称、共享类型、共享内容说明以及使用条款（可选）。 

    ![EnterShareDetails](./media/enter-share-details.png "输入共享详细信息") 

1. 选择“继续”。

1. 若要向共享添加数据集，请选择“添加数据集”。 

    ![向共享添加数据集](./media/datasets.png "数据集")

1. 选择要添加的数据集类型。 你将会看到一个不同的数据集类型列表，具体取决于你在上一步中选择的共享类型（快照或就地）。 如果从 Azure SQL 数据库或 Azure Synapse Analytics（以前称为 Azure SQL DW）进行共享，系统会提示你选择身份验证方法以列出表。 选择“AAD 身份验证”，然后选中“允许‘数据共享’以我的名义运行上述‘创建用户’脚本”复选框。 

    ![AddDatasets](./media/add-datasets.png "添加数据集")    

1. 导航到要共享的对象，选择“添加数据集”。 

    ![SelectDatasets](./media/select-datasets.png "选择数据集")    

1. 在“接收者”选项卡中，选择“+ 添加接收者”，输入数据使用者的电子邮件地址。 

    ![AddRecipients](./media/add-recipient.png "添加收件人") 

1. 选择“继续”。

1. 如果已选择了快照共享类型，则可以配置快照计划来向数据使用者提供数据更新。 

    ![EnableSnapshots](./media/enable-snapshots.png "启用快照") 

1. 选择开始时间和重复周期间隔。 

1. 选择“继续”。

1. 在“查看 + 创建”选项卡中查看包内容、设置、接收者和同步设置。 选择“创建”。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 运行 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令创建 Data Share：

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. 使用 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 命令为上一命令中的 Data Share 创建一个容器：

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. 运行 [az datashare create](/cli/azure/datashare#az_datashare_create) 命令创建 Data Share：

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. 使用 [az datashare invitation create](/cli/azure/datashare/invitation#az_datashare_invitation_create) 命令为指定地址创建邀请：

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

你的 Azure Data Share 现已创建，Data Share 的接收者现已准备好接受你的邀请。

## <a name="clean-up-resources"></a>清理资源

当不再需要该资源时，转到“数据共享概述”页面并选择“删除”将其删除 。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建 Azure Data Share 并邀请接收者。 若要了解数据使用者如何接受和接收数据共享，请继续学习接受和接收数据教程。

> [!div class="nextstepaction"]
> [教程：使用 Azure Data Share 接受和接收数据](subscribe-to-data-share.md)
