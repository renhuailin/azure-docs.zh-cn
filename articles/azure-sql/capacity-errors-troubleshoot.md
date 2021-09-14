---
title: 解决 Azure SQL 资源的容量错误
description: 了解在尝试部署或缩放 Azure SQL 数据库或 Azure SQL 托管实例资源时，如何解决可能出现的容量错误。
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: mathoma
ms.date: 09/03/2021
ms.custom: references_regions
ms.openlocfilehash: 885be735055eaf65d67466694e65d6cf7fdf00da
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481403"
---
# <a name="resolve-capacity-errors-with-azure-sql-database-or-azure-sql-managed-instance"></a>解决 Azure SQL 数据库或 Azure SQL 托管实例的容量错误
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

本文介绍如何在部署 Azure SQL 数据库或 Azure SQL 托管实例资源时解决容量错误。 

## <a name="exceeded-quota"></a>超过配额 

如果在尝试部署 Azure SQL 资源时遇到以下任何错误，请[请求增加配额](database/quota-increase-request.md)： 

- `Server quota limit has been reached for this location. Please select a different location with lower server count.`
- `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx.`
- 在缩放操作期间，你可能会看到以下错误：    
  `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx. `. 

## <a name="subscription-access"></a>订阅访问权限

如果订阅尚未向 SQL 资源提供程序 (RP) 注册，则订阅可能无权在所选区域中创建服务器。  

如果看到以下错误，请[向 SQL RP 注册订阅](#register-with-sql-rp)：
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`


## <a name="enable-region"></a>启用区域 

如果尚未启用所选区域，则订阅可能无法在该区域创建服务器。 若要解决此问题，请提交[支持请求，为订阅启用特定区域](database/quota-increase-request.md#region)。 

如果看到以下错误，请提交支持票证以启用特定区域： 
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`



## <a name="register-with-sql-rp"></a>注册 SQL RP

若要部署 Azure SQL 资源，请将订阅注册到 SQL 资源提供程序 (RP)。 

可使用 Azure 门户、[Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps) 注册订阅。 

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中注册订阅，请执行以下步骤： 

1. 打开 Azure 门户，转到“所有服务”。
1. 转到“订阅”，选择感兴趣的订阅。
1. 在“订阅”页上，选择“设置”下的“资源提供程序”。
1. 在筛选器中输入“sql”，以便显示与 SQL 相关的扩展。
1. 根据所需操作，为 Microsoft.Sql 提供程序选择“注册”、“重新注册”或“取消注册”   。

   ![修改提供程序](./media/capacity-errors-troubleshoot/register-with-sql-rp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

若要使用 [Azure CLI](/cli/azure/install-azure-cli) 注册订阅，请运行以下 cmdlet：

```azurecli-interactive
# Register the SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMac 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

若要使用 [Azure PowerShell](/powershell/azure/install-az-ps) 注册订阅，请运行以下 cmdlet： 

```powershell-interactive
# Register the SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.Sql

```

---

## <a name="additional-provisioning-issues"></a>其他预配问题

如果仍然遇到预配问题，请在 SQL 数据库的支持主题下打开区域访问请求，并指定要在 Azure SQL 数据库或 Azure SQL 托管实例上使用的 DTU 或 vCore。 

## <a name="azure-program-regions"></a>Azure 计划区域 

Azure 计划产品/服务可访问一组有限的区域，这些产品/服务包括 Azure Pass、Imagine、面向学生的 Azure、MPN、BizSpark、BizSpark Plus、适用于初创企业的 Microsoft/赞助产品/服务、Visual Studio 订阅/MSDN。 

如果你的订阅是 Azure 计划产品/服务的一部分，并且你想请求访问以下任何区域，请考虑改用备用区域： 

澳大利亚中部、澳大利亚中部 2、澳大利亚东南部、巴西东南部、加拿大东部、中国东部、中国北部、中国北部 2、法国南部、德国北部、日本西部、Jio 印度中部、JIO 印度西部、韩国南部、挪威西部、南非西部、印度南部、瑞士西部、阿拉伯联合酋长国中部、英国西部、US DoD 中部、US DoD 东部、US Gov 亚利桑那州、美国中西部、印度西部。 

## <a name="next-steps"></a>后续步骤

提交请求后，可查看该请求。 系统会根据你在表单中提供的信息与你联系。

有关其他 Azure 限制的详细信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
