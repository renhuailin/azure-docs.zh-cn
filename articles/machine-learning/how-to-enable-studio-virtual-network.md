---
title: 在虚拟网络中启用 Azure 机器学习工作室
titleSuffix: Azure Machine Learning
description: 了解如何配置 Azure 机器学习工作室来访问存储在虚拟网络内部的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperf-fy20q4, tracking-python
ms.openlocfilehash: bf4a019c9f40475750fd508a56f7f8903e0a2876
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111538857"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>在 Azure 虚拟网络中使用 Azure 机器学习工作室

本文介绍如何在虚拟网络中使用 Azure 机器学习工作室。 工作室包括 AutoML、设计器和数据标签等功能。 若要在虚拟网络中使用这些功能，必须遵循本文中的步骤。

在本文中，学习如何：

> [!div class="checklist"]
> - 授予工作室访问存储在虚拟网络内部的数据的权限。
> - 从虚拟网络内部的资源访问工作室。
> - 了解工作室如何影响存储安全性。

本文是由五部分组成的系列文章的第五部分，指导你如何保护 Azure 机器学习工作流。 强烈建议你通读前面的部分，以设置虚拟网络环境。

请参阅本系列中的其他文章：

[1.VNet 概述](how-to-network-security-overview.md) > [2.保护工作区](how-to-secure-workspace-vnet.md) > [3.保护训练环境](how-to-secure-training-vnet.md) > [4.保护推理环境](how-to-secure-inferencing-vnet.md) > **5.启用工作室功能**


> [!IMPORTANT]
> 如果工作区位于主权云（如 Azure 政府或 Azure 中国世纪互联）中，则集成笔记本不支持使用虚拟网络中的存储空间。 但你可以从计算实例使用 Jupyter Notebook。 有关详细信息，请参阅[访问计算实例笔记本中的数据](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook)部分。

## <a name="prerequisites"></a>先决条件

+ 阅读[网络安全概述](how-to-network-security-overview.md)，了解常见的虚拟网络方案和体系结构。

+ 要使用的预先存在的虚拟网络和子网。

+ 现有的[启用了专用链接的 Azure 机器学习工作区](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)。

+ 现有的[已添加虚拟网络的 Azure 存储帐户](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)。

## <a name="configure-data-access-in-the-studio"></a>在工作室中配置数据访问权限

在虚拟网络中，默认情况下会禁用某些工作室功能。 若要重新启用这些功能，必须为计划在工作室中使用的存储帐户启用托管标识。 

在虚拟网络中，默认情况下禁用以下操作：

* 预览工作室中的数据。
* 在设计器中将数据可视化。
* 在设计器中部署模型（[默认存储帐户](#enable-managed-identity-authentication-for-default-storage-accounts)）。
* 提交 AutoML 试验（[默认存储帐户](#enable-managed-identity-authentication-for-default-storage-accounts)）。
* 启动标记项目。

工作室支持从虚拟网络中的以下数据存储类型读取数据：

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL 数据库

### <a name="firewall-settings"></a>防火墙设置

一些存储服务（如 Azure 存储帐户）有防火墙设置，该设置适用于该特定服务实例的公共终结点。 通常情况下，可以通过该设置来允许/禁止从公共 Internet 的特定 IP 地址进行的访问。 使用 Azure 机器学习工作室时，不支持此功能。 使用 Azure 机器学习 SDK 或 CLI 时，支持此功能。

> [!TIP]
> 使用 Azure 防火墙服务时，支持 Azure 机器学习工作室。 有关详细信息，请参阅[使用防火墙后面的工作区](how-to-access-azureml-behind-firewall.md)。

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>将数据存储配置为使用工作区托管标识

将 Azure 存储帐户添加到具有[服务终结点](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)或[专用终结点](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)的虚拟网络后，必须配置数据存储才能使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)身份验证。 这样，工作室就可以访问存储帐户中的数据。

Azure 机器学习使用[数据存储](concept-data.md#datastores)连接到存储帐户。 使用以下步骤，将数据存储配置为使用托管标识：

1. 在工作室中，选择“数据存储”。

1. 若要更新现有数据存储，请选择相应的数据存储并选择“更新凭据”。

    若要创建新的数据存储，请选择“+ 新建数据存储”。

1. 在数据存储设置中，对于“在 Azure 机器学习工作室中使用工作区托管标识进行数据预览和分析”，选择“是” 。

    ![显示如何启用托管工作区标识的屏幕截图](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

这些步骤使用 Azure RBAC 将工作区托管标识作为“读取者”添加到存储服务。 “读取者”访问权限允许工作区查看资源，但不能进行更改。

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>对默认存储账户启用托管标识身份验证

每个 Azure 机器学习工作区都有两个默认存储帐户：一个默认的 Blob 存储帐户和一个默认的文件存储帐户，这两个帐户都是在创建工作区时定义的。 还可以在“数据存储”管理页面中设置新的默认值。

![显示可在何处查找默认数据存储的屏幕截图](./media/how-to-enable-studio-virtual-network/default-datastores.png)

下表说明了必须为工作区默认存储帐户启用托管标识身份验证的原因。

|存储帐户  | 说明  |
|---------|---------|
|工作区默认 Blob 存储| 存储设计器中的模型资源。 必须在此存储帐户上启用托管标识身份验证才能在设计器中部署模型。 <br> <br> 如果设计器管道使用已配置为使用托管标识的非默认数据存储，则可以可视化和运行该设计器管道。 但如果未在默认数据存储上启用托管标识就尝试部署定型模型，则无论是否正在使用任何其他数据存储，部署都会失败。|
|工作区默认文件存储| 存储 AutoML 试验资产。 必须在此存储帐户上启用托管标识身份验证才能提交 AutoML 试验。 |

> [!WARNING]
> 但有一个已知问题，即默认文件存储不会自动创建 `azureml-filestore` 文件夹，而提交 AutoML 试验又需要此文件夹。 当用户在创建工作区的过程中将现有文件存储设置为默认文件存储时，就会发生这种情况。
> 
> 有两个选项可避免此问题：1) 使用在创建工作区的过程中系统自动为你创建的默认文件存储。 2) 若要使用自己的文件存储，请在创建工作区的过程中确保文件存储位于 VNet 外部。 创建工作区后，再将存储帐户添加到虚拟网络。
>
> 若要解决此问题，请从虚拟网络中删除文件存储帐户，然后将其重新添加到虚拟网络。

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>授予工作区托管标识对存储专用链接的 __读取者__ 访问权限

如果 Azure 存储帐户使用专用终结点，则必须授予工作区托管标识对专用链接的“读取者”访问权限。 有关详细信息，请参阅[读取者](../role-based-access-control/built-in-roles.md#reader)内置角色。 

如果你的存储帐户使用服务终结点，则可以跳过此步骤。

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>从 VNet 内部的资源访问工作室

如果要从虚拟网络内的资源（例如，计算实例或虚拟机）访问工作室，则必须允许从虚拟网络到工作室的出站流量。 

例如，如果使用网络安全组 (NSG) 来限制出站流量，请将一条规则添加到 __服务标记__ 目标 __AzureFrontDoor.Frontend__。

## <a name="technical-notes-for-managed-identity"></a>托管标识的技术说明

使用托管标识访问存储服务会影响安全注意事项。 本部分介绍每种存储帐户类型的更改。 

这些注意事项是专门针对你要访问的存储帐户类型的。

### <a name="azure-blob-storage"></a>Azure Blob 存储

对于 Azure Blob 存储，还会将工作区托管标识添加为 [Blob 数据读取者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)，以便它能够从 Blob 存储读取数据。

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 访问控制

你可以使用 Azure RBAC 和 POSIX 样式的访问控制列表 (ACL) 来控制虚拟网络内的数据访问。

若要使用 Azure RBAC，请将工作区托管标识添加到 [Blob 数据读取者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色。 有关详细信息，请参阅 [Azure 基于角色的访问控制](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)。

若要使用 ACL，可以向工作区托管标识分配访问权限，就像向任何其他安全主体分配访问权限一样。 有关详细信息，请参阅[文件和目录上的访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 访问控制

Azure Data Lake Storage Gen1 仅支持 POSIX 样式的访问控制列表。 可以向工作区托管标识分配对资源的访问权限，就像向任何其他安全主体分配访问权限一样。 有关详细信息，请参阅 [Azure Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)。

### <a name="azure-sql-database-contained-user"></a>Azure SQL 数据库包含用户

若要使用托管标识访问存储在 Azure SQL 数据库中的数据，必须创建一个映射到托管标识的 SQL 包含用户。 若要详细了解如何从外部提供程序创建用户，请参阅[创建映射到 Azure AD 标识的包含用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。

创建 SQL 包含用户后，使用 [GRANT T-SQL 命令](/sql/t-sql/statements/grant-object-permissions-transact-sql)向该用户授予权限。

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Azure 机器学习设计器中间模块输出

可以在设计器中指定任何模块的输出位置。 使用此功能可将中间数据集存储在单独的位置，以用于安全性、日志记录或审核目的。 指定输出：

1. 选择要指定其输出的模块。
1. 在右侧出现的模块设置窗格中，选择“输出设置”。
1. 指定要用于每个模块输出的数据存储。
 
确保有权访问虚拟网络中的中间存储帐户。 否则，管道将会失败。

还应该为中间存储帐户[启用托管标识身份验证](#configure-datastores-to-use-workspace-managed-identity)，以可视化输出数据。

## <a name="next-steps"></a>后续步骤

虚拟网络系列文章由五部分构成，本文是第五部分。 若要了解如何保护虚拟网络，请参阅其余文章：

* [第 1 部分：虚拟网络概述](how-to-network-security-overview.md)
* [第 2 部分：保护工作区资源](how-to-secure-workspace-vnet.md)
* [第 3 部分：保护训练环境](how-to-secure-training-vnet.md)
* [第 4 部分：保护推理环境](how-to-secure-inferencing-vnet.md)

另请参阅有关使用[自定义 DNS](how-to-custom-dns.md) 进行名称解析的文章。
