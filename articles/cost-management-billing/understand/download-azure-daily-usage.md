---
title: 查看和下载 Azure 使用情况与费用
description: 了解如何下载或查看 Azure 每日使用情况和费用，并查看其他可用资源。
keywords: 计费使用情况, 使用费, 使用情况下载, 查看使用情况, Azure 发票, Azure 使用情况
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.date: 07/28/2020
ms.openlocfilehash: b5f1090c11844e1ca75c2296275588e5123a9d92
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737021"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>查看和下载 Azure 使用情况与费用

可以在 Azure 门户中下载 Azure 使用情况和费用的每日明细。 还可使用 Azure CLI 获取使用情况数据。 只有某些角色（例如帐户管理员或企业管理员）有权获取 Azure 使用情况信息。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](../manage/manage-billing-access.md)。

如果签订了 Microsoft 客户协议 (MCA)，则必须是计费对象信息所有者、参与者、读者或发票管理者，才能查看 Azure 使用情况和费用。  如果签订了 Microsoft 合作伙伴协议 (MPA)，则只有合作伙伴组织中的全局管理员和管理员代理角色才能查看和下载 Azure 使用情况和费用。

根据使用的订阅类型，用于下载使用情况和费用的选项有所不同。

## <a name="download-usage-from-the-azure-portal-csv"></a>从 Azure 门户下载使用情况 (.csv)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。  
    ![屏幕截图显示在 Azure 门户中搜索“成本管理 + 计费”。](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. 根据你的访问权限，可能需要选择一个计费帐户或计费对象信息。
1. 在左侧菜单中，选择“账单”  下的“发票”  。
1. 在发票网格中，找到与要下载的使用情况对应的计费周期行。
1. 选择右侧的下载图标或省略号 (`...`)  。  
  ![屏幕截图显示带有下载选项的“成本管理 + 计费发票”页面。](./media/download-azure-daily-usage/download-usage-others.png)  
1. “下载”窗格将在右侧打开。 在“使用情况详细信息”部分，选择“下载”   。  

## <a name="download-usage-for-ea-customers"></a>下载 EA 客户的使用情况数据

若要作为 EA 客户查看和下载使用情况数据，你必须是启用了“查看费用策略”的企业管理员、帐户所有者或部门管理员。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。  
    ![屏幕截图显示 Azure 门户搜索。](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. 如果有权访问多个计费帐户，请为 EA 计费帐户选择计费范围。
1. 选择“使用情况 + 费用”。 
1. 针对要下载的月份，选择“下载”。  
    ![屏幕截图显示 EA 客户的“成本管理 + 计费发票”页面。](./media/download-azure-daily-usage/download-usage-ea.png)

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>下载 Microsoft 客户协议使用情况

若要查看和下载计费对象信息的使用数据，你必须是计费对象信息所有者、参与者、读者或发票管理者。

### <a name="download-usage-for-billed-charges"></a>下载计费费用的使用情况

1. 搜索“成本管理 + 计费”。 
2. 选择计费对象信息。
3. 选择“发票”  。
4. 在发票网格中，找到与要下载的使用情况对应的发票行。
5. 单击行末尾的省略号 (`...`)。
6. 在下载上下文菜单中，选择“Azure 使用情况和费用”  。

### <a name="download-usage-for-open-charges"></a>下载未决费用的使用情况

还可以下载当前计费周期（这意味着尚未计费）的本月至今使用情况。

1. 搜索“成本管理 + 计费”。 
2. 选择计费对象信息。
3. 在“概览”边栏选项卡中，单击“下载 Azure 使用情况与费用”。  

### <a name="download-usage-for-pending-charges"></a>下载未决费用的使用情况

如果签订了 Microsoft 客户协议，则可以下载当前计费周期的每月至今使用量。 这些尚未计费的使用费。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 搜索“成本管理 + 计费”。
3. 选择计费对象信息。 根据你的访问权限，可能需要先选择一个计费帐户。
4. 在“概述”区域中，找到“最近费用”下的下载链接。
5. 选择“下载使用量和价格”。

## <a name="get-usage-data-with-azure-cli"></a>使用 Azure CLI 获取使用情况数据

首先为 Azure CLI 准备环境：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

登录后，请使用 [az costmanagement query](/cli/azure/costmanagement#az_costmanagement_query) 命令来查询订阅本月至今的使用情况信息：

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

还可以使用 --dataset-filter 参数或其他参数来缩小查询范围：

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" \
   --dataset-filter "{\"and\":[{\"or\":[{\"dimension\":{\"name\":\"ResourceLocation\",\"operator\":\"In\",\"values\":[\"East US\",\"West Europe\"]}},{\"tag\":{\"name\":\"Environment\",\"operator\":\"In\",\"values\":[\"UAT\",\"Prod\"]}}]},{\"dimension\":{\"name\":\"ResourceGroup\",\"operator\":\"In\",\"values\":[\"API\"]}}]}"
```

--dataset-filter 参数采用 JSON 字符串或 `@json-file`。

还可以选择使用 [az costmanagement export](/cli/azure/costmanagement/export) 命令将使用情况数据导出到 Azure 存储帐户。 可从此处下载数据。

1. 创建一个资源组或使用现有资源组。 若要创建资源组，请运行 [az group create](/cli/azure/group#az_group_create) 命令：

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. 可以创建一个存储帐户或使用现有存储账户来接收导出。 若要创建帐户，请使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令：

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. 运行 [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) 命令以创建导出：

   ```azurecli
   az costmanagement export create --name DemoExport --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --storage-directory demodirectory
   ```

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要详细了解发票和使用费用，请参阅：

- [了解 Microsoft Azure 详细使用条款](understand-usage.md)
- [了解 Microsoft Azure 账单](review-individual-bill.md)
- [查看和下载 Microsoft Azure 发票](download-azure-invoice.md)
- [查看和下载组织的 Azure 定价](../manage/ea-pricing.md)

如果你签订了 Microsoft 客户协议，请参阅：

- [了解有关 Microsoft 客户协议 Azure 详细使用情况的术语](mca-understand-your-usage.md)
- [了解 Microsoft 客户协议发票上的费用](review-customer-agreement-bill.md)
- [查看和下载 Microsoft Azure 发票](download-azure-invoice.md)
- [查看和下载 Microsoft 客户协议税单](mca-download-tax-document.md)
- [查看和下载组织的 Azure 定价](../manage/ea-pricing.md)
