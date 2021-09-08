---
title: 将应用程序配置存储区移动到其他区域
description: 了解如何将应用程序配置存储区移动到其他区域。
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: c3ffe773e16eb7a658f219e980711eca2add1aac
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123077281"
---
# <a name="move-an-app-configuration-store-to-another-region"></a>将应用程序配置存储区移动到其他区域 

应用程序配置存储区是特定于区域的，不能自动跨区域移动。 必须在目标区域中创建新的应用程序配置存储区，然后将内容从源存储移到新目标存储。 由于各种原因，你可能需要将配置移动到其他区域。 例如，为了利用具有可用性区域支持的新的 Azure 区域，为了部署仅在特定区域中可用的功能或服务，或为了满足内部策略和监管要求。 

以下步骤将引导你完成创建新的目标存储区并将当前存储导出到新区域的过程。 

## <a name="design-considerations"></a>设计注意事项

在开始之前，请注意理解以下概念：

* 配置存储区名称是全局唯一的。 
* 需要在新的配置存储区中重新配置访问策略和网络配置设置。

## <a name="create-the-target-configuration-store"></a>创建目标配置存储区

### <a name="portal"></a>[Portal](#tab/portal)     
若要在门户中创建新的应用程序配置存储区，请执行以下步骤： 
1.  登录 [Azure 门户](https://portal.azure.com)。 在主页的左上角，选择“创建资源”  。 在“搜索市场”框中，输入“应用配置”，然后选择 Enter<kbd></kbd>。 

    ![搜索应用配置](../../includes/media/azure-app-configuration-create/azure-portal-search.png)
1. 在搜索结果中选择“应用程序配置”，然后选择“创建”   。

    ![选择“创建”](../../includes/media/azure-app-configuration-create/azure-portal-app-configuration-create.png)
1. 在“创建应用配置”窗格中，输入以下设置：
    
    | 设置 | 建议的值 | 说明 |
    |---|---|---|
    | **订阅** | 你的订阅 | 选择原始存储区的 Azure 订阅 |
    | **资源组** | 你的资源组 | 选择原始存储区的 Azure 资源组 |
    | **资源名称** | 全局唯一名称 | 输入要用于目标应用程序配置存储区的唯一资源名称。 此名称不能与上一个配置存储区的名称相同。 |
    | **位置** | 目标位置 | 选择要将配置存储区移动到的目标区域。 |
    | **定价层** | *标准* | 选择所需的定价层。 有关详细信息，请参阅[应用配置定价页](https://azure.microsoft.com/pricing/details/app-configuration)。 |
1. 选择“查看 + 创建”以验证自己的设置  。
1. 选择“创建”。 部署可能需要几分钟。
1. 部署资源后，请重新创建源存储区的访问策略和网络配置设置。 这些不会随配置一起传输。 这可能包括使用管理标识、虚拟网络和公用网络访问。 
    
#### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
若要在 CLI 中创建新的应用程序配置存储区，请执行以下步骤： 
1. 使用你的凭据登录到 Azure CLI。
    ```azurecli
    az login
    ```
1. 通过 `create` 命令创建新配置存储区，
    ```azurecli
    az appconfig create -g MyResourceGroup -n MyResourceName -l targetlocation --sku Standard 
    ```
    并输入以下设置：

    | 设置 | 建议的值 | 说明 |
    |---|---|---|
    | **资源组** | 你的资源组 | 选择原始存储区的 Azure 资源组 |
    | **资源名称** | 全局唯一名称 | 输入要用于目标应用程序配置存储区的唯一资源名称。 此名称不能与上一个配置存储区的名称相同。 |
    | **位置** | 目标位置 | 选择要将配置存储区移动到的目标区域。 |
    | **Sku** | *标准* | 选择所需的定价层。 有关详细信息，请参阅[应用配置定价页](https://azure.microsoft.com/pricing/details/app-configuration)。 |
1. 部署可能需要几分钟。 完成后，请重新创建源存储的访问策略和网络配置设置。 这些不会随配置值一起传输。 这可能包括使用管理标识、虚拟网络和公用网络访问。 有关详细信息，请参阅 [CLI 文档](./cli-samples.md)。
---

## <a name="transfer-your-configuration-key-values"></a>传输配置键-值  

### <a name="portal"></a>[Portal](#tab/portal)
请按照以下步骤使用门户将配置导出到目标存储：
1. 在 [Azure 门户](https://portal.azure.com)中导航到源配置存储区，并选择“操作”下的“导入/导出” 。
1. 选择“导出”，然后选择“目标服务”下拉列表中的“应用程序配置”  。 
    ![导出到其他配置存储区](media/export-to-config-store.png)
1. 单击“选择资源”，并输入“订阅”和“资源组”  。 “资源”是先前创建的目标配置存储区的名称。 
1. 选择“应用”以验证目标配置存储区。 
1. 将“从标签”、“时间”和“标签”字段保留为默认值，然后选择“应用”。 
1. 若要验证是否已成功将配置从源传输到目标存储区，请在门户中导航到目标配置存储区。 选择“操作”下的“配置资源管理器”，然后验证这是否包含和原始存储区中相同的键值对 。 
    > [!NOTE]
    > 此过程仅允许一次由一个标签导出配置键值。 若要导出多个，请对每个标签重复步骤 2-5。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
请按照以下步骤使用 Azure 将配置导出到目标存储：
1. 在 Azure CLI 中，输入以下命令，将源配置存储区中的所有值导出到目标配置存储区。 
    ```azurecli
    az appconfig kv export -n SourceConfigurationStore -d appconfig --dest-name TargetConfigurationStore --key * --label * --preserve-labels
    ```
1. 若要验证是否已成功将配置从源传输到目标存储区，请列出目标存储区中的所有键值。 
    ```azurecli
    az appconfig kv list -n TargetAppConfiguration --all
    ```
---
## <a name="delete-your-source-configuration-store"></a>删除源配置存储区 

如果配置已传输到目标存储区，则可以选择删除源配置存储区。 

### <a name="portal"></a>[Portal](#tab/portal)
请按照以下步骤在门户中删除源配置存储区：
1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。
1. 在“按名称筛选”框中，输入资源组的名称。 
1. 在结果列表中，选择资源组名称以查看概述。
1. 选择源配置存储区，然后在“概述”边栏选项卡上，选择“删除” 。 
1. 系统会提示你确认是否要删除配置存储区，请选择“是”。

几分钟后，源配置存储区将被删除。

### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
请按照以下步骤在 Azure CLI 中删除源配置存储区：
1. 在 Azure CLI 中运行以下命令： 
    ```azurecli
    az appconfig delete -g ResourceGroupName -n SourceConfiguration
    ```
    请注意“资源组”与源配置存储区相关联。 
1. 删除源配置存储区可能需要一段时间。 可以通过列出资源组中的所有当前配置存储区来验证操作是否成功。 
    ```azurecli
    az appconfig list -g MyResourceGroup
    ```
    几分钟后，源配置存储区将被删除。

---
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从 Azure 应用程序配置存储区自动备份键值](./howto-move-resource-between-regions.md)
>[Azure 应用程序配置复原能力和灾难恢复](./concept-disaster-recovery.md)
