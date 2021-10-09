---
title: 教程：接受和接收数据 - Azure Data Share
description: 教程 - 使用 Azure Data Share 接受和接收数据
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 09/10/2021
ms.openlocfilehash: 035b6c9b12df69395409c1c0aa70be219bc2533e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760187"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>教程：使用 Azure Data Share 接受和接收数据  

本教程介绍如何使用 Azure Data Share 接受数据共享邀请。 另外还介绍如何接收共享给你的数据，以及如何启用定期刷新时间间隔，确保共享给你的数据快照始终是最新的。 

> [!div class="checklist"]
> * 如何接受 Azure Data Share 邀请
> * 创建 Azure Data Share 帐户
> * 指定数据目标
> * 创建按计划刷新的数据共享的订阅

## <a name="prerequisites"></a>先决条件
在接受数据共享邀请之前，必须预配多个 Azure 资源，这些资源已在下面列出。 

确保在接受数据共享邀请之前已满足所有先决条件。 

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* Data Share 邀请：Microsoft Azure 发出的邀请，主题名为“来自 **<yourdataprovider@domain.com>** 的 Azure Data Share 邀请”。
* 在你将在其中创建 Data Share 资源的 Azure 订阅以及目标 Azure 数据存储所在的 Azure 订阅中注册 [Microsoft.DataShare 资源提供程序](concepts-roles-permissions.md#resource-provider-registration)。

### <a name="receive-data-into-a-storage-account"></a>将数据接收到存储帐户

* 一个 Azure 存储帐户：如果没有，可以创建一个 [Azure 存储帐户](../storage/common/storage-account-create.md)。 
* 向存储帐户进行写入的权限，此权限位于 *Microsoft.Storage/storageAccounts/write* 中。 “参与者”角色有此权限。 
* 向存储帐户添加角色分配的权限，此权限存在于 *Microsoft.Authorization/role assignments/write* 中。 “所有者”角色有此权限。  

### <a name="receive-data-into-a-sql-based-target"></a>将数据接收到基于 SQL 的目标中
如果选择将数据接收到 Azure SQL 数据库和 Azure Synapse Analytics，请查看以下相关先决条件的列表。 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>将数据接收到 Azure SQL 数据库或 Azure Synapse Analytics（以前称为 Azure SQL DW）的先决条件

* Azure SQL 数据库或 Azure Synapse Analytics（以前称为 Azure SQL DW）。
* 向 SQL 服务器上的数据库进行写入的权限，此权限存在于 *Microsoft.Sql/servers/databases/write* 中。 “参与者”角色有此权限。 
* SQL 服务器的 Azure Active Directory 管理员
* SQL Server 防火墙访问权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中的 SQL Server 中，导航到“防火墙和虚拟网络” 
    1. 对于“允许 Azure 服务和资源访问此服务器”，单击“是”。
    1. 单击“+ 添加客户端 IP”。 客户端 IP 地址可能会更改。 下次从 Azure 门户共享 SQL 数据时，可能需要重复此过程。 还可以添加 IP 范围。
    1. 单击“ **保存**”。 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>将数据接收到 Azure Synapse Analytics（工作区）SQL 池的先决条件

* Azure Synapse Analytics（工作区）专用 SQL 池。 目前不支持将数据接收到无服务器 SQL 池中。
* 在 Synapse 工作区中写入 SQL 池的权限，该权限位于 Microsoft.Synapse/workspaces/sqlPools/write。 “参与者”角色有此权限。
* Data Share 资源的托管标识用于访问 Synapse 工作区 SQL 池的权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中导航到 Synapse 工作区。 从左侧导航栏中选择“SQL Active Directory 管理员”，并将你自己设置为“Azure Active Directory 管理员”。
    1. 打开 Synapse Studio，从左侧导航栏中选择“管理”。 在“安全性”下选择“访问控制”。 为自己分配“SQL 管理员”或“工作区管理员”角色 。
    1. 在 Synapse Studio 中，从左侧导航栏中选择“开发”。 在 SQL 池中执行以下脚本，以将 Data Share 资源托管标识添加为“db_datareader, db_datawriter, db_ddladmin”。 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       请注意， *<share_acc_name>* 是 Data Share 资源的名称。 如果尚未创建 Data Share 资源，则可以稍后返回到该先决条件。  

* Synapse 工作区防火墙访问权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中导航到 Synapse 工作区。 从左侧导航栏中选择“防火墙”。
    1. 对于“允许 Azure 服务和资源访问此工作区”，单击“启用”。
    1. 单击“+ 添加客户端 IP”。 客户端 IP 地址可能会更改。 下次从 Azure 门户共享 SQL 数据时，可能需要重复此过程。 还可以添加 IP 范围。
    1. 单击“ **保存**”。 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>将数据接收到 Azure 数据资源管理器群集中： 

* 数据提供程序的数据资源管理器群集所在的同一 Azure 数据中心内的 Azure 数据资源管理器群集：如果没有此群集，可以创建一个 [Azure 数据资源管理器群集](/azure/data-explorer/create-cluster-database-portal)。 如果你不知道数据提供程序群集的 Azure 数据中心，可以稍后在此过程中创建群集。
* 向 Azure 数据资源管理器群集进行写入的权限，此权限存在于 *Microsoft.Kusto/clusters/write* 中。 “参与者”角色有此权限。 

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="open-invitation"></a>打开邀请

### <a name="portal"></a>[门户](#tab/azure-portal)

1. 可以从电子邮件或直接从 Azure 门户打开邀请。 

   若要从电子邮件打开邀请，请检查收件箱中是否有来自数据提供程序的邀请。 邀请来自 Microsoft Azure，标题为“来自 <yourdataprovider@domain.com> 的 Azure Data Share 邀请”。  单击“查看邀请”，以在 Azure 中查看邀请。  

   若要直接从 Azure 门户打开邀请，请在 Azure 门户中搜索“数据共享邀请”  。 此操作会转到“Data Share 邀请”列表。

   如果你是租户的来宾用户，则在首次查看 Data Share 邀请之前，系统会要求你验证租户的电子邮件地址。 验证后，它将在 12 个月内有效。

   ![邀请列表](./media/invitations.png "邀请列表") 

1. 选择要查看的邀请。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

准备 Azure CLI 环境，然后查看邀请信息。

首先为 Azure CLI 准备环境：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

运行 [az datashare consumer invitation list](/cli/azure/datashare/consumer/invitation#az_datashare_consumer_invitation_list) 命令查看当前邀请：

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

复制你的邀请 ID 以便在下一部分中使用。

---

## <a name="accept-invitation"></a>接受邀请

### <a name="portal"></a>[门户](#tab/azure-portal)

1. 确保查看所有字段，包括“使用条款”。  如果同意使用条款，则必须勾选表示同意的框。 

   ![使用条款](./media/terms-of-use.png "使用条款") 

1. 在“目标 Data Share 帐户”下选择要在其中部署 Data Share 的订阅和资源组。  

   对于“Data Share 帐户”字段，如果没有现有的 Data Share 帐户，请选择“新建”。   否则，请选择一个现有的 Data Share 帐户来接受数据共享。 

   对于“已接收共享的名称”字段，可以保留数据提供者指定的默认值，也可以为已接收共享指定新名称  。 

   同意使用条款并指定用于管理已接收共享的数据共享帐户以后，请选择“接受并配置”。 将创建共享订阅。 

   ![接受选项](./media/accept-options.png "接受选项") 

   此操作会将你转到 Data Share 帐户中接收的共享。 

   如果不想接受邀请，请选择“拒绝”。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az datashare consumer share-subscription create](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_create) 命令创建 Data Share。

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>配置已接收共享

### <a name="portal"></a>[门户](#tab/azure-portal)

按照以下步骤配置要接收数据的位置。

1. 选择“数据集”选项卡。选中要为其分配目标位置的数据集旁边的复选框。 选择“+ 映射到目标”以选择目标数据存储。 

   ![映射到目标](./media/dataset-map-target.png "映射到目标") 

1. 选择以哪种目标数据存储类型保存数据。 目标数据存储中具有相同路径和名称的任何数据文件或表将被覆盖。 如果要将数据接收到 Azure SQL 数据库或 Azure Synapse Analytics（以前称为 Azure SQL DW）中，请选中复选框“允许‘数据共享’以我的名义运行上述‘创建用户’脚本”。

   对于就地共享，请选择指定“位置”中的数据存储。 “位置”是数据提供程序的源数据存储所在的 Azure 数据中心。 映射数据集之后，可以通过目标路径中的链接来访问数据。

   ![目标存储帐户](./media/dataset-map-target-sql.png "目标存储") 

1. 对于基于快照的共享，如果数据提供程序已创建定期更新数据的快照计划，你还可以通过选择“快照计划”选项卡来启用快照计划。选中快照计划旁边的框，然后选择“+ 启用”。 请注意，第一个计划快照将在计划时间的一分钟内启动，后续快照将在计划时间的几秒内启动。

   ![启用快照计划](./media/enable-snapshot-schedule.png "启用快照计划")

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用这些命令配置要接收数据的位置。

1. 运行 [az datashare consumer share-subscription list-source-dataset](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_dataset) 命令获取数据集 ID：

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. 运行 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令，为此 Data Share 创建存储帐户：

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. 使用 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 命令获取存储帐户 ID：

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. 使用以下命令获取帐户主体 ID：

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. 使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 命令为帐户主体创建角色分配：

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. 基于数据集 ID 为映射创建变量：

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. 使用 [az datashare consumer dataset-mapping create](/cli/azure/datashare/consumer/dataset-mapping#az_datashare_consumer_dataset_mapping_create) 命令创建数据集映射：

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. 运行 [az datashare consumer share-subscription synchronization start](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_start) 命令启动数据集同步。

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   运行 [az datashare consumer share-subscription synchronization list](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_list) 命令查看同步的列表：

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   使用 [az datashare consumer share-subscription list-source-share-synchronization-setting](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) 命令查看在你的共享上设置的同步设置。

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>触发快照

### <a name="portal"></a>[门户](#tab/azure-portal)

这些步骤仅适用于基于快照的共享。

1. 可以通过依次选择“详细信息”选项卡、“触发快照”来触发快照 。 在这里，可以触发数据的完整或增量快照。 如果是首次从数据提供者处接收数据，请选择完整副本。 

   ![触发快照](./media/trigger-snapshot.png "触发快照") 

1. 如果上次运行状态为“成功”，请转到目标数据存储来查看接收的数据。 选择“数据集”，然后单击“目标路径”中的链接。 

   ![使用者数据集](./media/consumer-datasets.png "使用者数据集映射") 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

运行 [az datashare consumer trigger create](/cli/azure/datashare/consumer/trigger#az_datashare_consumer_trigger_create) 命令来触发快照：

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> 仅将此命令用于基于快照的共享。

---

## <a name="view-history"></a>查看历史记录
此步骤仅适用于基于快照的共享。 若要查看快照历史记录，请选择“历史记录”选项卡。在这里可以找到包含过去 30 天生成的所有快照的历史记录。

## <a name="clean-up-resources"></a>清理资源

当不再需要该资源时，转到“数据共享概述”页面并选择“删除”将其删除 。

## <a name="next-steps"></a>后续步骤
本教程介绍了如何接受和接收 Azure Data Share。 若要详细了解 Azure Data Share 概念，请继续了解 Azure Data Share 术语。

> [!div class="nextstepaction"]
> [Azure Data Share 概念](terminology.md)